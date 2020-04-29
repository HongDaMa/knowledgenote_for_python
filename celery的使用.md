## 1.celery

celery是一个基于Python开发的模块，可以帮助我们对任务进行分发和处理<img src="C:\Users\Mahongda\AppData\Roaming\Typora\typora-user-images\1588079969136.png" alt="1588079969136" style="zoom:100%;" />

### 1.1环境的搭建

```
pip3 install celery
按照broker --> redis 或者 rabbitMQ
```

### 1.2快速使用

- s1.py

  ```python
  from celery Celery
  
  app = Celery('tasks',borker='192.168.10.48:6379',backend='redis://192.168.10.48:6379')
  
  @app.task
  def x1(x,y):
  	return x+y
  
  @app.task
  def x2(x,y):
  	return x-y
  ```

- s2.py

  ```python
  from s1 import x1
  
  result =x1.delay(4,4)
  print(result.id)
  ```

- s3.py

  ```
  from celery.result import AsyncResult
  from s1 import app
  
  result_object = AsyncResult(id="xxxxxxxx",app=app)
  print(result_object.status)
  ```

运行程序：

1.启动redis

2.启动work

```
#进入目录
celery worker -A s1 -l info
```

windows:

```
Traceback (most recent call last):
  File "e:\py_virtual_env\celery_env\lib\site-packages\billiard\pool.py", line 362, in workloop
    result = (True, prepare_result(fun(*args, **kwargs)))
  File "e:\py_virtual_env\celery_env\lib\site-packages\celery\app\trace.py", line 546, in _fast_trace_task
    tasks, accept, hostname = _loc
ValueError: not enough values to unpack (expected 3, got 0)

解决办法：
pip3 install eventlet

celery worker -A s1 -l info -P eventlet
```

3,创建任务

```
python s2.py
```

4.查看任务状态

```python
#填写任务ID
python s3.py
```

### 1.3 django-celery

在django中应用celery,为了方便使用安装django-celery模块

```
pip3 django-celery
```

#### 1.3.1 基本使用目录结构

```
`django_celery_demo``├── app01``│  ├── __init__.py``│  ├── admin.py``│  ├── apps.py``│  ├── migrations``│  ├── models.py``│  ├── tasks.py``│  ├── tests.py``│  └── views.py``├── db.sqlite3``├── django_celery_demo``│  ├── __init__.py``│  ├── celery.py``│  ├── settings.py``│  ├── urls.py``│  └── wsgi.py``├── manage.py``├── red.py``└── templates`
```

#### 1.3.2 第一步：[项目/项目/Settings.py]添加Django-Celery全局配置：

```python
# ######################## Celery配置 ########################
CELERY_BROKER_URL = 'redis://10.211.55.20:6379'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_RESULT_BACKEND = 'redis://10.211.55.20:6379'
CELERY_TASK_SERIALIZER = 'json'
```

#### 1.3.3  第二步：[项目/项目/celery.py]在项目同名目录创建celery.py

```python
import os
from celery import Celery

# set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE随便写一个', '.settings')
#去配置文件中读取配置
app = Celery('django_celery_demo')

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
# - namespace='CELERY' means all celery-related configuration keys
#   should have a `CELERY_` prefix.
#定义以后所有得celery在配置文件中都是以"CELERT_"开头得
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django app configs.
#去每个已注册的APP中读取tasks.py文件
app.autodiscover_tasks()
```

#### 1.3.4 第三步：[项目/app名称/tasks.py]在任意app中创建task.py

```python
from celery import shared_task

@shared_task
def x1(x,y):
	return x+y

@shared_task
def x2(x,y):
	return x-y
```

#### 1.3.5 第四步:[项目/项目/__init__.py]

```python
from .celery import app as celery_app
__all__ = ('celery_app',)
```

#### 1.3.6 编写视图函数，调用celery去创建任务

- url

  ```python
  url(r'^create/task/$'),task.create_task)
  url(r'^get/result/$'),task.get_result)
  ```

- 视图函数

  ```
  from django.shortcuts import HttpResponse
  from api.tasks import x1
  
  def create_task(request):
  	print('请求来了')
  	result = x1.del;ay(2,2)
  	print('执行完毕')
  	return HttpResponse(result.id)
  	
  def get_result(request):
  	nid = request.GET.get('nid')
  	from celery.result import AsyResult
  	from demo import celery_app
  	result_object =AsyResult(id=nid,app=celery_app)
  	data = result_object.get()
  	return HttpResponse(data)
  ```

#### 1.3.7 启动worker

```
进入项目目录
celery worker -A demos -l info -P eventlet
```

#### 1.3.8 启动django程序

```
python manage.py ...
```

### 1.4 celery定时执行

创建定时任务

```python
import datetime

def create_task(request):
    #定时执行
    #获取本地时间
    ctime = datetime.datetime.now()
    # 本地时间转换成utc时间
    utc_ctime = datetime.datetime.utcfromtime.stamp(ctime.timestamp())
    target_time = utc_ctime + datetime.timedelta(seconds=10)
    result = x1.apply_async(args=[11,3],eta=target_time)
    
    return HttpResponse(result.id)

```

查看任务进度

```python
def get_result(request):
	nid = request.GET.get('nid')
	from celery.result import AsyncResult
	from demos import celery_app
	result_object = AsyncResult(id=nid,app=celery_app)
	
    #print(result_object.status) #获取状态
    #data = result_object.get() #获取数据
    #result_object.forget() #把数据在backend中移除
    
    #取消任务
    #result_object.revoke()
    if result_object.successful():
        result_object.get()
        result_object.forget() #清空任务结果
    elif result_object.failed():
        pass
    else
    	pass
    return HttpResponse('...')
```



