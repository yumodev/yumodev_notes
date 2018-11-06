## Gradle 执行sh脚步文件



```
task upload(type: Exec) {
    executable 'sh'
    args "-c","../文件名.sh $参数1 $参数2"
}
```

