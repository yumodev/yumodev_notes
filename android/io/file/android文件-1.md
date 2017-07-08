1、android文件目录为
   /data/data/<package name>/files。
   可以在Activity中通过getFilesDir()方法获取该目录
2、android的缓存目录为：
   /data/data//<package name>/cache
   可以在Activity中通过getCacheDir()方法获取该目录
3、在Activity中操作/data/data/<package name>/files目录下的文件。
  FielInputStream in = this.getContext().openFileInput("a.txt");
  FielOutputStream out = this.getContext().openFileOutput("a.txt");