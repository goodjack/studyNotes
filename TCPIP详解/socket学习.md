### Socket

socket 是应用层与TCP/IP 协议族通信的中间软件抽象层，它是一组接口。在设计模式中，socket 是一个门面模式，它把复杂的 TCP/IP 协议族隐藏在 socket 接口后面，让 Socket 去实现符合指定的协议。

### php socket 函数

| 函数                 | 说明                             |
| -------------------- | -------------------------------- |
| socket_create()      | 创建一个 socket 本地资源         |
| socket_bind()        | 绑定地址和端口                   |
| socket_listen()      | 监听地址和端口                   |
| socket_set_option()  | 在绑定前设置 socket 参数         |
| socket_getpeername() | 获取远程端的ip 和 port           |
| socket_accept()      | 创建一个远程连接资源             |
| socket_select()      | 用于 IO 多路复用（多客户端连接） |

PHP socket 聊天室实现

```php
<?php
class Ws
{
    // 握手加密参数
    protected $mask = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';

    // 服务端
    public $master = null;

    // 客户端数组
    public $sockets = [];
    public $write = null;
    public $except = null;

    public $users = [];

    protected $readLength = 1024;

    public function __construct($host,$port)
    {
        $this->master = socket_create(AF_INET,SOCK_STREAM,SOL_TCP);
        socket_set_option($this->master, SOL_SOCKET, SO_REUSEADDR, true);
        socket_bind($this->master, $host, $port);
        socket_listen($this->master);
        $this->sockets[] = $this->master;
        $this->connection();
    }

    protected function connection()
    {
        while (true) {
            /*
             * 此处是避免 socket_select 传入的参数是引用传值，会修改外面的值，把 sockets 数组
             * 复制给 $read 这样引用传参就不会影响到 $sockets 这个数组
            */
            $tmp_sockets = $this->sockets;

            // 此函数会选中当前活跃的连接资源，就是 $read 会变成一个只包含当前活跃连接的数组
            socket_select($tmp_sockets, $this->write, $this->except, null);

            foreach ($tmp_sockets as $socket) {
                // 如果当前活跃连接等于初始 $socket 则表示是一个新建连接
                if ($socket === $this->master) {
                    $newClient = socket_accept($this->master);
                    $this->sockets[] = $newClient;
                    $this->users[] = ['client'=>$newClient,'handleShake' => false];
                } else {
                    $readMsg = socket_read($socket,$this->readLength);
                    $key = $this->currentClient($socket);
                    // 如果 $readMsg 长度等于 8 则表示客户端断开了连接
                    if (strlen($readMsg) == 8) {
                        $this->close($key);
                    }
                    if (!$this->users[$key]['handleShake']) {
                        $this->handleShake($socket);
                        $this->users[$key]['handleShake'] = true;
                    } else {
                        $this->send($readMsg,$key);
                    }
                }
            }
        }
    }

    // 当客户端退出关闭连接
    protected function close($key)
    {
        socket_close($this->users[$key]['client']);
        unset($this->users[$key]);
        $this->sockets = null;
        $this->sockets[] = $this->master;
        foreach ($this->users as $user) {
            $this->sockets[] = $user['client'];
        }
    }

    protected function send($msg,$key)
    {
        $user = json_decode($this->decode($msg),true);
        if ($user['type'] == 'login') {
            $res['type'] = 'login';
            $res['status'] = 'success';
            $res['msg'] = "{$user['name']}：登录成功";

            $this->users[$key]['name'] = $user['name'];

            $userNames['name'] = $this->getUserNames();
            $userNames['type'] = 'userLists';

            $response = $this->encode(json_encode($userNames));

            foreach ($this->users as $v) {
                socket_write($v['client'],$response,strlen($response));
            }
        }

        $response = $this->encode(json_encode($res));

        foreach ($this->users as $v) {
            socket_write($v['client'],$response,strlen($response));
        }
    }

    private function getUserNames()
    {
        foreach ($this->users as $user) {
            $users[] = $user['name'];
        }

        return $users;
    }

    protected function currentClient($socket)
    {
        foreach ($this->users as $k => $v) {
            if ($socket == $v['client']) {
                return $k;
            }
        }
    }

    protected function handleShake($currentUser)
    {
        $request = socket_read($currentUser,$this->readLength);

        $key = null;
        if (preg_match('/Sec-WebSocket-Key: (.*)\r\n/', $request, $match)) {
            $key = $match[1];
        }

        $encryKey = base64_encode(sha1($key . $this->mask, true));

        $response = "HTTP/1.1 101 Switching Protocols\r\n";
        $response .= "Upgrade: websocket\r\n";
        $response .= "Connection: Upgrade\r\n";
        $response .= "Sec-WebSocket-Accept: {$encryKey}\r\n\r\n";

        socket_write($currentUser,$response,strlen($response));
    }

    // 编码
    protected function encode($msg)
    {
        $frame = [];
        $frame[0] = '81';
        $len = strlen($msg);
        if ($len < 126) {
            $frame[1] = $len < 16 ? '0' . dechex($len) : dechex($len);
        } elseif ($len < 65025) {
            $s = dechex($len);
            $frame[1] = '7e' . str_repeat('0', 4 - strlen($s)) . $s;
        } else {
            $s = dechex($len);
            $frame[1] = '7f' . str_repeat('0', 16 - strlen($s)) . $s;
        }
        $data = '';
        $l = strlen($msg);
        for ($i = 0; $i < $l; $i++) {
            $data .= dechex(ord($msg{$i}));
        }
        $frame[2] = $data;
        $data = implode('', $frame);
        return pack("H*", $data);
    }

    // 解码
    protected function decode($buffer)
    {
        $len = $masks = $data = $decoded = null;
        $len = ord($buffer[1]) & 127;

        if ($len === 126) {
            $masks = substr($buffer, 4, 4);
            $data = substr($buffer, 8);
        } else if ($len === 127) {
            $masks = substr($buffer, 10, 4);
            $data = substr($buffer, 14);
        } else {
            $masks = substr($buffer, 2, 4);
            $data = substr($buffer, 6);
        }
        for ($index = 0; $index < strlen($data); $index++) {
            $decoded .= $data[$index] ^ $masks[$index % 4];
        }
        return $decoded;
    }
}

new Ws('php-fpm',8888);

```

