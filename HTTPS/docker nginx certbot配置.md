使用一个独立的容器来生成 certbot 证书：

```dockerfile
FROM nignx:alpine;
RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo Asia/Shanghai > /etc/timezone
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/' /etc/apk/repositories
RUN apk add --no-cache certbot
```



使用一个脚本自动生成：

```sh
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
case $1 in 
    "generate")
        docker run --rm -v $HOME/www/letsencrypt:/etc/letsencrypt \
                    -v $HOME/www:/var/www \ 
                    --name cert mycertbot:1.0 \
                    certbot certonly --webroot -w /var/www \
                    -d zhu-zi.top -d www.zhu-zi.top \
                    -m 1872314654@qq.com --agree-tos --non-interactive
                    ;;
            
    "renew")
        docker run --rm -v $HOME/www/letsencrypt:/etc/letsencrypt \
                        -v $HOEM/www:/var/www \
                        --name cert mycertbot:1.0 \
                        certbot renew
                        docker exec dnmp_nginx_1 bash -c 'nginx -s reload'
                        ;;
esac
exit 0

```

写入 crontab :

```shell
0 2 * */3 * /bin/bash $HOME/Dnmp/cerbot/run-cerbot.sh renew 2 >> $HOME/Dnmp/cerbot/error.log
```

生成证书时，要确保 `.well-known` 目录可以访问，不然没法验证