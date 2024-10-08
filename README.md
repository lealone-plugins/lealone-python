# lealone-python

在 Lealone 中使用 Python 语言开发微服务应用或编写用户自定义函数


## 编译需要

* jdk 17+
* maven 3.8+


## 打包插件

运行 `mvn clean package -Dmaven.test.skip=true`

生成 jar 包 `target/lealone-python-plugin-6.0.1.jar`

假设 jar 包的绝对路径是 `/home/lealone/lealone-plugins/python/target/lealone-python-plugin-6.0.1.jar`


## 创建插件

先参考[ lealone 快速入门](https://github.com/lealone/Lealone-Docs/blob/master/应用文档/Lealone数据库快速入门.md) 启动 lealone 数据库并打开一个命令行客户端

然后执行以下命令创建插件：

```sql
create plugin python
  implement by 'com.lealone.plugins.python.PythonServiceExecutorFactory' 
  class path '/home/lealone/lealone-plugins/python/target/lealone-python-plugin-6.0.1.jar';
```

要 drop 插件可以执行以下命令：

```sql
drop plugin python;
```

执行 drop plugin 会把插件占用的内存资源都释放掉



## 使用 Python 开发微服务应用

/home/lealone/lealone-plugins/python/src/test/resources/python/hello_service.py

```Python
def hello(name):
    return "hello " + name;
```


## 创建服务

执行以下 SQL 创建 python_hello_service

```sql
create service if not exists python_hello_service (
  hello(name varchar) varchar
)
language 'python' implement by '/home/lealone/lealone-plugins/python/src/test/resources/python/hello_service.py';
```

也可以用以下简化版本，无需声明服务的方法，会自动调用 py 文件里定义的方法

```sql
create service if not exists python_hello_service
language 'python' implement by '/home/lealone/lealone-plugins/python/src/test/resources/python/hello_service.py';
```


## 调用服务

执行以下 SQL 就可以直接调用 python_hello_service 了

```sql
sql> execute service python_hello_service hello('test');
+-------------------------+
| 'PYTHON_HELLO_SERVICE.HELLO()' |
+-------------------------+
| hello test              |
+-------------------------+
(1 row, 2 ms)

sql>
```


