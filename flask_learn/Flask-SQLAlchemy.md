## Flask-SQLAlchemy

Flask-SQLAlchemy是Flask的一个ORM插件。

### 简单入门使用

* 简易导入

  ```python
  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy

  app = Flask(__name__)
  app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
  db = SQLAlchemy(app)

  class User(db.Model):
      # 连接的表的信息
      __tablename__ = 'xxx'  # 表需要手动创建
      __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}
      
      # 数据标准
      id = db.Column(db.Integer, primary_key=True)
      username = db.Column(db.String(80), unique=True)
      email = db.Column(db.String(120), unique=True)

      def __init__(self, username, email):
          self.username = username
          self.email = email

      def __repr__(self):
          return '<User %r>' % self.username
  ```

  不同的数据库连接url

  ```
  # Postgres
  postgresql://scott:tiger@localhost/mydatabase
  # MySQL
  mysql://scott:tiger@localhost/mydatabase
  # Oracle
  oracle://scott:tiger@127.0.0.1:1521/sidname
  # SQLite 
  sqlite:////absolute/path/to/foo.db
  ```


* 根据模型创建数据库表

  ```python
  from yourapplication import db
  db.create_all()
  ```


* 添加用户

  ```python
  user = User('xxxx', 'xxxx')
  db.session.add(user)
  db.session.commit()
  # 要提交事务才会真正地写入到数据库
  ```


* 查询

  ```python
  # 读取所有用户
  users = User.query.all()
  # 搜索username为xxx的第一个结果
  user = User.query.filter_by(username = 'xxxx').first()
  ```



### 数据操作

* 插入

  ```python
  from yourapp import User
  user = User('xxxx', 'xxxx')
  db.session.add(user)
  db.session.commit()
  ```

* 删除记录

  ```python
  db.session.selete(user)
  db.session.commit()
  ```

* 查询

  ````python
  user = User.query.filter_by(username='xxxx').first()
  # 当不存在的时候返回None

  users = User.query.filter(User.email.endswith('@qq.com')).all()
  # 返回符合要求的所有用户

  users = User.query.order_by(User.username)
  # 返回结果按照username排序

  users = User.query.limit(1).all()
  # 限制返回用户的数量

  users = User.query.get(1)
  # 用主键查询用户
  ````

* 在视图中查询

  当您编写 Flask 视图函数，对于不存在的条目返回一个 404 错误是非常方便的。因为这是一个很常见的问题，Flask-SQLAlchemy 为了解决这个问题提供了一个帮助函数。可以使用 get_or_404() 来代替 get()，使用 first_or_404() 来代替 first()。这样会抛出一个 404 错误，而不是返回 None:

  ```python
  @app.route('/user/<username>')
  def show_user(username):
      user = User.query.filter_by(username=username).first_or_404()
      return render_template('show_user.html', user=user)
  ```

  ​

###  引用上下文

想要使用不止一个应用或者在一个函数中动态地创建应用需要init_app()

```python
from flask import Flask
from flask.ext.sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app():
    app = Flask(__name__)
    db.init_app(app)
    return app
```

