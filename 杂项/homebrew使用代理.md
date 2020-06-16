```bash
ALL_PROXY=socks5://127.0.0.1:1080 brew install/search xxx  # 这样子就可以使用代理
```



**brew 从本地安装文件**

下载 brew 安装失败的文件，执行 `brew --cache` 将下载的文件移动到 `brew --cache ` 的地址中，再执行原命令即可。