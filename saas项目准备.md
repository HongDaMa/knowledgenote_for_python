# 1.虚拟环境virtualenv

## 1.1安装

```
pip3 install virtualenv
```

## 1.2创建虚拟环境

```
virtualenv 环境名称
#注意：创建[环境名称] 文件夹，放置所有的环境。进入指定目录
```

```
假设：目前电脑 py27/py35
virtualenv 环境名称 --python=python3.6
virtualenv 环境名称 --python='C\python\python3.6.exe'

virtualenv 环境名称 --python=python2.7
```

```
1.打开终端
2.安装：virtualenv
	pip3 install virtualenv
3.终端关闭，在重新打开
4.通过命令进入指定目录
	win:
		>>>D:
		>>>cd envs
	mac:
		...
5.创建虚拟环境
	virtualenv s38
```

## 1.3激活虚拟环境

```
win:
	>>> cd Scripts 进入虚拟环境 Scripts 目录
	>>> activate 激活虚拟环境
mac:
	>>> source s25/bin/activate
```

```
win:
	>>> cd Scripts 进入虚拟环境 Scripts 目录
	>>> deactivate 退出虚拟环境
mac:
	>>> 任意目录 deactivate
```

## 1.4 在虚拟环境中安装模块

- 激活虚拟环境

- 在激活的虚拟环境中安装模块

  ```
  pip3 install django==1.11.28
  
  pip3 install django==1.11.28 -i http//pypi.douban.com/simple --trusted host pypi,douban.com
  ```

  

# 2.搭建项目环境（django+虚拟环境）

![1587184732076](E:\python个人知识拓展笔记\image\1587184732076.png)

![1587184782836](E:\python个人知识拓展笔记\image\1587184782836.png)

![1587184810084](E:\python个人知识拓展笔记\image\1587184810084.png)

# 3.本地配置local_setting.py

local_setting.py

## 3.1在setting中导入

```python
try:
	from .local_settings import *
except ImportError:
	pass
```

## 3.2创建本地配置

```python
#!E:\py_virtual_env\saas_project\Scripts\python.exe
# -*- coding: utf-8 -*-

LANGUAGE_CODE = 'zh-hans'
```

**切记**：给别人代码时，不要给他local_settings

# 4.给别人代码

![1587186838477](E:\python个人知识拓展笔记\image\1587186838477.png)

## 4.1创建一个远程仓库（gitee）

![1587187394236](C:\Users\Mahongda\AppData\Roaming\Typora\typora-user-images\1587187394236.png)



![1587187559514](E:\python个人知识拓展笔记\image\1587187559514.png)

## 4.2本地代码推送到git

- 让git忽略一些文件 .gitignore

  ```python
  #pycharm
  .idea/
  
  __pycache__/
  *.py[cod]
  *$py.class
  
  #Django stuff
  local_settings.py
  *.sqllite3
  
  #database migrations
  */migrations/*.py
  !*/migrations/__init__.py
  ```

- 让git管理项目

```powershell
(saas_project) E:\python项目\sass_project>git init
Initialized empty Git repository in E:/python项目/sass_project/.git/

(saas_project) E:\python项目\sass_project>git add .

(saas_project) E:\python项目\sass_project>git commit -m '项目第一次提交'
[master (root-commit) 2f87ddc] '项目第一次提交'
 13 files changed, 217 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 app01/__init__.py
 create mode 100644 app01/admin.py
 create mode 100644 app01/apps.py
 create mode 100644 app01/migrations/__init__.py
 create mode 100644 app01/models.py
 create mode 100644 app01/tests.py
 create mode 100644 app01/views.py
 create mode 100644 manage.py
 create mode 100644 sass_project/__init__.py
 create mode 100644 sass_project/settings.py
 create mode 100644 sass_project/urls.py
 create mode 100644 sass_project/wsgi.py
```

- git本地项目推送到远程仓库

  ```shell
  #给远程仓库地址取一个别名：origin
  git remote add origin https://gitee.com/mahongda/saas_project.git
  #推送到远程仓库
  #gitee:
  git push origin master
  #github:
  git push -u origin_github master
  ```

  ## 4.2测试获取代码

https://gitee.com/mahongda/saas_project.git

```shell
进入想要放代码的目录
git clone https://gitee.com/mahongda/saas_project.git
```

```
1.git软件，本地进行版本管理
2.码云/github/gitlab,代码托管
```

### .gitignore的作用

```
	git软件在本地做版本控制的时候会忽略掉，在提交到远程仓库的时候就没有这个被忽略掉的文件了。
```

### 虚拟环境的作用

```
项目之间环境隔离。
开发：本地环境
线上：多个环境进行隔离
```

### 生成本地虚拟环境配置：

```shell
#讲当前环境中所有的模块写入到requriement.txt
pip3 freeze > requriement.txt
#批量安装requriement.txt中的所有模块
pip3 install -r requriement.txt
```

