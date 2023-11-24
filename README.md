# 天天生鲜网站

一个生鲜网站，使用python+django3开发，支付功能使用支付宝API，定时任务使用celery，全文检索使用whoosh、haystack，中文分词使用jieba。

django3.2.1



## 一、开发环境

* Windows11

* Python:3.9.13
* Django:3.2.1
* Visual Studio Code



### 二、项目模块列表

1. 用户模块
2. 商品模块
3. 订单模块
4. 购物车模块



### 三、项目所需服务列表

1. mysql

   ​	用来存储用户信息、商品信息以及订单信息

2. redis

   ​	内存型数据库，主要充当数据缓存，用来存储session、用户购物车的商品id等

3. FastDFS

   ​	文件存储服务，用来存储图片文件，然后在mysql数据库里存储文件的url地址

4. nginx

   ​	web服务器，常用来做负载均衡，充当指挥使。

5. celery

   ​	异步处理的任务队列，用来异步处理邮件发送。

6. uwsgi

   ​	本项目中用来实现后台运行程序。



### 四、说明：

1. 前端的页面文件也已经打包好了，如果是新手的话，请使用下面这个命令来安装所需依赖包。

```
pip install -r requirements.txt -i http://mirrors.aliyun.com/pypi/simple --trusted-host mirrors.aliyun.com
```

2. fast_client这个模块我已经放在tools目录下了，你可以自己解压文件来安装，使用这个命令

```
pip install setup.py install
```

​	或者把fdfs_client这个文件夹复制到你的开发环境的site-packages目录下，然后就可以导入了。

3. 全文检索haystack对中文支持不好，修改后的文件也放在tools目录下了，请将ChineseAnalyzer.py和whoosh_cn_backend.py放置在site-packages\haystack\backends目录下。



### 五、启动命令

启动准备，修改settings.py

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'dailyfresh',
        'USER': 'root',
        'PASSWORD': 'mysql0220',
        'HOST': 'localhost',
        'PORT': 3306
    }
}

# redis的缓存配置
CACHES = {"default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://localhost:6379/9",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}

FDFS_URL = 'http://localhost:8000/'
```

#### 迁移数据库

```
python manage.py makemigrations
python manage.py migrate
```

#### 启动

```
python manage.py runserver
```



### 六、问题

Django3 用户认证系统authenticate()一直返回None的分析及解决
1.在账号注册的时候，插入函数要弄对，要用   objects.create_user()  函数，你用objects.create插入的时明文的，authenticate()当然会读取不到。

    create_user()这个函数会将密码自动加密，而不是明文存储。
    
    在这里 这个create_user()函数  需要用形参的形式，才能成功。
    
    authenticate()会将输入的明文密码，自动加密，然后去数据库匹配，如果匹配成功返回一个user对象，如果不成功，返回None。

2.在settings.py文件中添加

```
# 让authenticate不关联is_active
AUTHENTICATION_BACKENDS = ['django.contrib.auth.backends.AllowAllUsersModelBackend']
```

python manage.py createsuperuser

```
fdfs_client 有个依赖库
pip install mutagen==1.30
pip install requests
```

```
http://127.0.0.1:8000/admin/
admin
12345678
```



#### 6.2 django模型类没更新

1.先看app目录下有无migrations，没有则创建
2.再看migrations目录下有无__init__.py，没有则创建
3.运行 python .\manage.py makemigrations，则成功编译，而不会显示No changes detected
4.再运行python .\manage.py migrate

python manage.py rebuild_index