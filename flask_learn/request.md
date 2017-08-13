## request

* 获取get的参数

  ```python
  from flask import request
  ...
  @app.route('/xxx', methods=['GET'])
  def xxx():
  	...
  	a = request.args.get('xxx')
  ```

* 获取post参数

  ```python
  from flask import request
  ...

  @app.route('/xxx', methods=['POST'])
  def xxx():
    ...
    r = request.form.get('xxx')
  ```

  ​