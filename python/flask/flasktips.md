## 错误

###  ImportError: No module named PIL 错误

`pip install Pillow`

### ImportError: No module named flask_sqlalchemy

`pip install flask_sqlalchemy`

### ImportError: No module named flaskext.wtf

flask0.9.0 之后，Flask-WTF will not import anything from wtforms, you need to import fields from wtforms.
所以你要这样：


```
from flask.ext.wtf import Form
from wtforms import TextField, BooleanField
from wtforms.validators import Required
```

`pip install Flask-WTF`

