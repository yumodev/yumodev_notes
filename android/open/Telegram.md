## 代理

* [使用内置服务器访问Telegram](https://github.com/sy618/hosts/blob/master/md/telegram.md)

## 参考

* [Telegram源码之android客户端配置](https://www.jianshu.com/p/013d15a817b1)

## tip

* NDK错误

`git submodule update --init --recursive`

* 提示找不到google-services.json

去[https://developers.google.com/mobile/add](https://developers.google.com/mobile/add)这个网站生成google-services.json文件

* slite3.c 找不到mmap

先注释掉相关内容
```c
//#if !defined(SQLITE_OMIT_WAL) || SQLITE_MAX_MMAP_SIZE>0
 // { "mmap",         (sqlite3_syscall_ptr)mmap,            0 },
//#else
 // { "mmap",         (sqlite3_syscall_ptr)0,               0 },
//#endif
```

