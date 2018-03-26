
##安装virtualenv

* sudo easy_install virtualenv
* sudo easy_install pip
* sudo pip install virtualenv

## 创建一个项目文件夹 python/venv

* mkdir python
* cd python
* virtualenv demo
  Installing setuptools, pip, wheel...done.
  
### 激活环境

* . demo/bin/activate

### 安装Flask

* pip install Flask

### 创建demo

* cd demo
* 创建文件:hello.py,输入下面内容：


```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()

```

### 运行应用

* `python hello.py` 输出下面内容：
  `* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)`
  
### 打开应用
 
 在浏览器中打开应用: `http://127.0.0.1:5000/`
 浏览器显示字符串：Hello World!

