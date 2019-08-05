# StreamingwithH5
H5与小程序直播开发 学习笔记



#### 安装 ffmpeg

##### Mac 系统

```
brew install ffmpeg
```



#### 安装 nginx

##### Mac 系统

```
brew install nginx-full --with-rtmp-module
```



#### 配置 nginx

```
nginx will load all files in /usr/local/etc/nginx/servers/.



\- Tips -

Run port 80:

 $ sudo chown root:wheel /usr/local/opt/nginx-full/bin/nginx

 $ sudo chmod u+s /usr/local/opt/nginx-full/bin/nginx

Reload config:

 $ nginx -s reload

Reopen Logfile:

 $ nginx -s reopen

Stop process:

 $ nginx -s stop

Waiting on exit process

 $ nginx -s quit



To have launchd start openresty/brew/nginx-full now and restart at login:

  brew services start openresty/brew/nginx-full

Or, if you don't want/need a background service you can just run:

  nginx



运行  nginx

➜   nginx

启动==>

http://localhost:8080/



配置：

➜  cd /usr/local/etc/nginx

➜  ls

fastcgi.conf           koi-win                scgi_params

fastcgi.conf.default   mime.types             scgi_params.default

fastcgi_params         mime.types.default     uwsgi_params

fastcgi_params.default nginx.conf             uwsgi_params.default

koi-utf                nginx.conf.default     win-utf

➜  open -e nginx.conf
```

> 配置推流功能后的nginx配置文件参见：[nginx配置文件](../Server/nginx.conf)







Reference From: https://github.com/cucygh/h5live