# 安装HomeBrew


```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

# mac 下常见的安装

* wget: brew install wget 

# bug解决

* Error: Cannot write to /usr/local/Cellar

```
查看权限:ls -ld /usr/local/Cellar
提升权限: sudo chmod a+w /usr/local/Cellar
```

