# Flask入门

## 最小应用


```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

## 设置外部可访问的服务器

app.run(host='0.0.0.0')

## 设置调试模式

app.debug = True 或者 app.run(debug=True)

## 路由

使用route()把装饰器把一个函数绑定到对应的URL上。
比如：

```
@app.route('/')
def hello_world():
    return 'Hello World!'
```

## 路由变量

给URL添加变量部分，用<variable_name>标记变量字段。这个部分将会作为命名参数传递到你的函数，可以使用<converter:variable_name>。转换器有：

int | 接受整数
float | 接受浮点数。
path | 接受斜线。


