## 非静默更新


```java
Intent intent = new Intent(Intent.ACTION_VIEW);  
intent.setDataAndType(Uri.parse("file://" + apkFilePath), "application/vnd.android.package-archive");  
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);  
mContext.startActivity(intent); 
```

## 静默安装

