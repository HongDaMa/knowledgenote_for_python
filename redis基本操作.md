# redis基本操作

## 1.安装redis

 https://www.pythonav.com/wiki/detail/10/82/ 

## 2.python操作redis的模块

 安装python操作redis模块 

```shell
pip3 install redis
```

 写代码去操作redis 

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import redis
# 直接连接redis
conn = redis.Redis(host='10.211.55.28', port=6379, password='foobared', encoding='utf-8')
# 设置键值：15131255089="9999" 且超时时间为10秒（值写入到redis时会自动转字符串）
conn.set('15131255089', 9999, ex=10)
# 根据键获取值：如果存在获取值（获取到的是字节类型）；不存在则返回None
value = conn.get('15131255089')
print(value)
```

 上面python操作redis的示例是以直接创建连接的方式实现，每次操作redis如果都重新连接一次效率会比较低，建议使用redis连接池来替换，例如： 

```
import redis
# 创建redis连接池（默认连接池最大连接数 2**31=2147483648）
pool = redis.ConnectionPool(host='10.211.55.28', port=6379, password='foobared', encoding='utf-8', max_connections=1000)
# 去连接池中获取一个连接
conn = redis.Redis(connection_pool=pool)
# 设置键值：15131255089="9999" 且超时时间为10秒（值写入到redis时会自动转字符串）
conn.set('name', "武沛齐", ex=10)
# 根据键获取值：如果存在获取值（获取到的是字节类型）；不存在则返回None
value = conn.get('name')
print(value)
```

## 3.django连接redis

按理说搞定上一步python代码操作redis之后，在django中应用只需要把上面的代码写到django就可以了。

例如：django的视图函数中操作redis

```python
import redis
from django.shortcuts import HttpResponse
# 创建redis连接池
POOL = redis.ConnectionPool(host='10.211.55.28', port=6379, password='foobared', encoding='utf-8', max_connections=1000)
def index(request):
    # 去连接池中获取一个连接
    conn = redis.Redis(connection_pool=POOL)
    conn.set('name', "武沛齐", ex=10)
    value = conn.get('name')
    print(value)
    return HttpResponse("ok")
```

 上述可以实现在django中操作redis。**但是**，这种形式有点非主流，因为在django中一般不这么干，而是用另一种更加简便的的方式。 

###  **第一步**：安装django-redis模块（内部依赖redis模块） 

 pip3 install django-redis 

###  **第二步**：在django项目的settings.py中添加相关配置 

```python
# 上面是django项目settings中的其他配置....
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://10.211.55.28:6379", # 安装redis的主机的 IP 和 端口
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "CONNECTION_POOL_KWARGS": {
                "max_connections": 1000,
                "encoding": 'utf-8'
            },
            "PASSWORD": "foobared" # redis密码
        }
    }
}
```

###  **第三步**：在django的视图中操作redis 

```python
from django.shortcuts import HttpResponse
from django_redis import get_redis_connection
def index(request):
    # 去连接池中获取一个连接
    conn = get_redis_connection("default")
    conn.set('nickname', "武沛齐", ex=10)
    value = conn.get('nickname')
    print(value)
    return HttpResponse("OK")
```

## 写在最后

至此，就是本节的所有内容，大家可以在django中通过redis进行存取值，在后续的项目开发中可以用他来完成短信验证码过期的功能。

以后关于redis还会讲很多其他高级的知识点，参见：

- https://pythonav.com/wiki/detail/3/33/
- https://www.cnblogs.com/wupeiqi/articles/5132791.html
- http://www.redis.cn/

