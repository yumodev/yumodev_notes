<h1 align="center" >Shadowsocks中Ubuntu中的使用 </h1>
## shadowsocks

### 安装shadowsocks

apt-get install python-pip 或者apt-get install python-gevent python-pip
pip install shadowsocks
apt-get install python-m2crypto

### 服务器端

#### 服务器端配置

在/etc/shadowsocks 目录下添加配置文件 config.json

```{
    "server":"xx.xx.xxx.xx",
    "server_port":8388,
    "local_port":1080,
    "password":"disheng",
    "timeout":600,
    "method":"aes-256-cfb"
}
```

#### 开启ShadowSocks

`ssserver -c /etc/shadowsocks/config.json -d start`

#### 关闭ShadowSocks

 `ssserver -c /etc/shadowsocks/config.json -d stop`
 

#### 开机自启动

  通过命令`sudo vi /etc/rc.local `
  打开rc.local文件,然后在exit前面加入下面这一行
`/usr/local/bin/ssserver -c /etc/shadowsocks/shadowsocks.json -d start`

### 客户端

#### 客户端配置
在/etc/shadowsocks 目录下添加配置文件 config.json
 
``` {
    "server":"xx.xx.xxx.xx",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1
}
```


#### 客户端启动

  `sslocal -c /etc/shadowsocks/config.json`

#### 开机自启动，

 在打开文件 ：`sudo vi /etc/rc.local`
 输入下面的命令：
  `sudo sslocal -c /etc/shadowsocks/config.json`

#### method rc4-md5 not supported

使用pip install shadowsocks安装，其启动项并没有放在/usr/bin下面，所以要手动的把运行程序放到/usr/bin，查看下载的shadowsocks，发现运行程序放在/home/用户名/.local/bin下面，将sslocal复制到/usr/bin下面

`sudo cp /home/用户名/.local/bin/sslocal /usr/bin`

## 终端中使用Shadowsocks

### Privoxy

#### 安装Privoxy

    `sudo apt-get install privoxy`
    
#### 配置

     sudo vi /etc/privoxy/config

     中间可找到本地的http接口
     listen-address  127.0.0.1:8118

     末尾输入
     forward-socks5 / 127.0.0.1:1080 .
     
#### 启动和停止

     sudo /etc/init.d/privoxy start
     sudo /etc/init.d/privoxy stop
    
#### 设置代理

    export http_proxy=http://localhost:1080
    export https_proxy=http://localhost:1080

    在/etc/profile的末尾添加

    export http_proxy="http://127.0.0.1:1080"
    export https_proxy="http://127.0.0.1:1080"





