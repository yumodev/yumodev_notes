## 安装Hexo

`npm install hexo-cli -g`

## 初始化blog


```
hexo init blog
cd blog
npm install
```

## 启动运行blog

通过`hexo s 或者 hexo s -i localhost
`命令启动blog，启动成功后会输出下面信息：

```
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```

## 预览blog

在浏览器输入网址：`http://localhost:4000 ` 打开blog

## Jekyll 迁移

把 _posts 文件夹内的所有文件复制到 source/_posts 文件夹，并在 _config.yml 中修改 new_post_name 参数。

`new_post_name: :year-:month-:day-:title.md`

## 切换皮肤


```
hexo clean
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

在_config.yml中的theme设置为：next

## 部署

* 安装插件： `npm install hexo-deployer-git`
* 修改配置：


```
 deploy:
  type: git
  repository: https://github.com/zhiho/zhiho.github.io.git
  branch: master
```

* 部署：`hexo deploy`



 

