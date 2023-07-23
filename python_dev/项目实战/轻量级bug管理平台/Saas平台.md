软件即服务
## 涉及的知识点：
1. 虚拟环境
2. local_settings.py本地配置
```python
##settings.py中的配置
try:
	from .localZ_settings import *
except ImportError:
	pass

```

```python
##local_settings.py中重写配置(自己开发的配置)
LANGUAGE_CODE = 'zh-hans'
DATABASES = {  
'default': {  
'ENGINE': 'django.db.backends.sqlite3',  
'NAME': '1.1.1.1',  
}  
}
```

```python

##settings.py中重写配置(正式的配置)
LANGUAGE_CODE = 'zh-hans'
DATABASES = {  
'default': {  
'ENGINE': 'django.db.backends.sqlite3',  
'NAME': '1.1.1.2',  
}  
}
```
3. 腾讯云平台（免费）
	- sms短信，申请服务
	- cos对象存储，云服务器的硬盘，项目中上传文件/查看文件/下载文件
4. redis
![[Pasted image 20230624133134.png]]
## 项目开发
- 一期：用户验证（短信验证，图片验证，djangoModelForm组件）- 3day
- 二期：wiki，文件，文件管理 - 5~7day
