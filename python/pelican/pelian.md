# Pelican

## 介绍

## 安装

```
virtualenv pelican
cd pelican
. bin/activate
git clone git://github.com/getpelican/pelican.git
cd pelican
python setup.py install
pip install Markdown
```

## 创建一个博客


```
mkdir testblog
cd testblog
pelican-quickstart
```


## 运行blog


```
pelican -i
http://localhost:8000/
```



## pelican 目录


```
├── Makefile
├── content #编辑文章存放目录
├── output #生成的静态文件目录
├── pelicanconf.py #配置文件
├── publishconf.py #配置文件
└── tasks.py
```


## 修改主题

* 选择主题

在[http://www.pelicanthemes.com/](http://www.pelicanthemes.com/)选择自己喜欢的主题

* 将主题下载到本地

`git clone git@github.com:getpelican/pelican-themes.git`

* 配置主题

在pelicanconf.py文件中，配置主题


```
THEME = "../pelican-themes/foundation-default-colours"
```


## 参考

* [用pelican搭建完美博客](https://www.cnblogs.com/imhurley/p/6137272.html)
* [\[译\]使用pelican搭建一个数据科学博客](https://www.jianshu.com/p/7caf9f023ab7)
* [Python/pelican搭建个人博客](https://www.jianshu.com/p/191019e3a710)

