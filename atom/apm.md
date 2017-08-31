### 安装apm命令

  通过`apm help install` 来检查是否安装了 `apm`。<br>
  如果没有安装，可以通过运行`Atom->Install Shell Commands` 菜单来安装 `apm`命令

### apm install

  * `apm install <package-name>` 安装最新版本
  * `apm install <package-name>@<package-version>`安装制定版本

### apm search

通过`apm search markdown` 查询包

### apm view

通过`apm search <package-name>` 查看相关的包的详细信息

### 直接git 下载安装(ubuntu/mac)，以run-in-browser插件为例说明。

　　####  首先找到插件的git地址。
　　　　　在Edit->Preference->Install Packages 搜到run-in-brwoser.然后双击打开。
　　　　　打开的网页为https://atom.io/packages/run-in-browser, 复制repo就是其仓库地址。
　　　　　https://github.com/michaelwayman/run-in-browser

　 #### 进入packages 目录。
　　　　　默认为 ~/.atom/packages . 可以Edit->Preference->Open Config Folder打开.atom目录，然后进入它的子目录:packages .
  #### 安装插件
  　　　git clone https://atom.io/packages/run-in-browser
      cd run-in-browser
      npm install

  #### 重启ATOM　完成安装。

  ### 修改插件的快捷键
