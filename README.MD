# Push RTMP Stream with Nginx Server in Ubuntu

## Software Envrioment config
Ubutnu 20.04.1LTS  
[Nginx 1.19.1](http://nginx.org/en/download.html)  
[FFmpeg 4.1.6](http://ffmpeg.org/doxygen/4.1/scaling_video_8c-example.html)  
[Github RTMP Modul](https://github.com/arut/nginx-rtmp-module)  

## Commands
1. FFmpeg install
```C
./config
sudo make -j4 && make install
```

2. Ubuntu install essential libraries
```C
apt-get install build-essential libpcre3 libpcre3-dev libssl-dev
```

3. Download current lastest package: nginx-1.19.1.tar.gz nginx-rtmp-module-master.zip  
*Dowloaded in ./dpkg-package/*  
*OR*  
```C
wget http://nginx.org/download/nginx-1.19.1.tar.gz
wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
```

4. Make project commands,install nginx and rtmp module
```C
cd nginx-1.19.1
./configure --with-http_ssl_module  --add-module=../nginx-rtmp-module-master
sudo make && make install
```

5. Nginx init commands  
nginx file come from [JasonGiedymin/nginx-init-ubuntu](https://github.com/JasonGiedymin/nginx-init-ubuntu)  
```C
chmod 777 /etc/init.d/nginx
update-rc.d nginxdefaults
```

6. Configure Rtemp stream  
Edit nginx.conf file. Add rtmp function, modify rtmp ports.  
```C
rtmp {  //add this functions
    server {  
        listen 1935;
        chunk_size 4096; 
        application live {
              live on;
              record off;
        }
    }  
}
``` 
```C
server {
	......
        listen       81; //modify this ports
        location / {
            root   /usr/share/nginx/html;  //modify this url
        }
	......
    }
```
Configure firewall, open rtmp ports.
```C
iptables -A INPUT -p tcp --dport 1935 -j ACCEPT 
iptables -A OUTPUT -p tcp --sport 1935 -j ACCEPT 
iptables -A INPUT -p tcp --dport 81 -j ACCEPT 
iptables -A OUTPUT -p tcp --sport 81 -j ACCEPT
```
Start Servers.
```C
service nginx start
gedit /usr/local/nginx/conf/nginx.conf
service nginx restart
```

7. Push Rtmp stream with FFmpeg
```C
sugar.mp4 (5mintues length)
ffmpeg -re -i sugar.mp4 -c copy -f flv rtmp://localhost:1935/live/sugar
```

## Server and Client connection Picture
1. Rtmp Server video by pushing Rtmp stream sends  
![Rtmp Server](http://m.qpic.cn/psc?/V53fRgvG3eglq316xcKz3bnj5O11sfmA/45NBuzDIW489QBoVep5mccGmJRUXXTH4lFI.Aq9KseXvwngBp1WQzi3Lp5a4RpzCKrhF3TltuOSUlUA3CBm4g3bfZcFhGYoiJMbqCVxWYGo!/b&bo=QAaEAwAAAAADN9M!&rf=viewer_4&t=5 "Ubutnu Rtmp Server")

2. Client likes VLC that gets video by recieving Rtmp video  
![VLC Client](http://m.qpic.cn/psc?/V53fRgvG3eglq316xcKz3bnj5O11sfmA/45NBuzDIW489QBoVep5mcXzo2vjtZx5CZPtocCwR2KL9vpaspGuJlarLXEFtT82WSM**iUs2S67mLLPo*UTVKab2w3SkEToOxrR1iEfyHUY!/b&bo=VgUAAwAAAAADVyI!&rf=viewer_4&t=5)

## Links  
[Ubuntu Nginx+Rtmp服务器搭建 （图文演示）](https://blog.csdn.net/avsys_z/article/details/79569279)  
[Github RTMP MODULE](https://github.com/arut/nginx-rtmp-module)  
