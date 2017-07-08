### 配置GoBuild

Tools->Build System->New Build System{
"shell_cmd": "make"
}
变成
{
"shell_cmd": "go run $file"
}

保存为goBuild

利用Command+B执行程序


