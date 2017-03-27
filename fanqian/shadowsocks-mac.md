<h1 align="center" >Mac终端下使用ShadowScoks </h1>
### Provixy

#### 安装 Provixy

    sudo brew install provixy

    安装日志：
``` ==>Downloading https://homebrew.bintray.com/bottles/privoxy-3.0.24.yosemite.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring privoxy-3.0.24.yosemite.bottle.1.tar.gz
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink sbin/privoxy
Target /usr/local/sbin/privoxy
already exists. You may want to remove it:
  rm '/usr/local/sbin/privoxy'

To force the link and overwrite all conflicting files:
  brew link --overwrite privoxy

To list all files that would be deleted:
  brew link --overwrite --dry-run privoxy

Possible conflicting files are:
/usr/local/sbin/privoxy
....
/usr/local/share/man/man1/privoxy.1
==> Caveats
To have launchd start privoxy now and restart at login:
  brew services start privoxy
Or, if you don't want/need a background service you can just run:
  privoxy /usr/local/etc/privoxy/config
==> Summary
🍺  /usr/local/Cellar/privoxy/3.0.24: 51 files, 1.6M

```

#### 配置

    sudo vi /usr/local/etc/privoxy/config

    中间可找到本地的http接口二选一
    listen-address 127.0.0.1:8118（只能本机访问）
    listen-address 0.0.0.0:8118()

    末尾输入
    forward-socks5 / 127.0.0.1:1080 .

### 启动Provixy

    /usr/local/sbin/privoxy /usr/local/etc/privoxy/config

### 验证启动成功与否

    netstat -an | grep 8118
    ps aux  | grep privoxy

### git 配置

	git config --global http.proxy 127.0.0.1:8118
	不适用
	git config --global --unset-all http.proxy
	
### 参考使用

  [Mac OS下使用Privoxy做中转代理](http://tblog.im/2015/09/23/shi-yong-privoxyzhong-zhuan/)
  [使用Privoxy做智能代理切换](http://blog.devtang.com/2012/12/08/use-privoxy/)


