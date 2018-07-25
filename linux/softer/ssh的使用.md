# ssh的使用

## 安装ssh-服务器端


* 安装

```
sudo apt-get install openssh-server
```

* 查询是否启动


```
ps -e | grep ssh
netstat -tlp 或者
```

* 启动


```
sudo /etc/init.d/ssh resart
```

## ssh使用

### 登陆

ssh 用户名@ip

### 发送和下载文件

* 从服务器下载文件 


```
scp <用户名>@<ssh服务器地址>:<文件路径> <本地文件名>
scp root@127.0.0.1:~/test.txt ~/Desktop/test.txt 
```

2. 上传文件到服务器 
 


```
10.220.120.119:scp <本地文件名> <用户名>@<ssh服务器地址>:<上传保存路径即文件名> 
```


