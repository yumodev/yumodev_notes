#目录相关操作。
1. mkdir
   该命令的作用是创建一个目录。它的常规使用方法为：

        mkdir testDir 创建一个空的名字为testDir目录。
   但是如果直接使用命令 *mkdir testDirA/testDirB* 创建testDirB
   目录的时候，如果testDirA目录不存在，那么就会创建testDirB目录失败。在用madir命令创建目录的时候，如果缺少中间目录可以使用"-p"选项，在创建目录的同时也同时创建了中间目录。命令范例如下：

		mkdir -p testDirA/testDirB
2. ls
	ls命令使用非常的频繁，它的作用就是列出目录里面的内容。

3. dir

4. vddir

5. rmdir

6. 