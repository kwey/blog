---
title: python中的装饰器
tags: 'cmd'
categories: 'python'
top_img: '/img/python.jpg'
---


- 函数外可以访问函数内的变量
## 写个简单的装饰器
``` python
def get_result(func):
    def warp(*args, **kwargs):
        print('haha warp')
        result = func(*args, **kwargs)
        print('result:', result)
        return result
    return warp

@get_result
def add(a, b):
    print('func add:', a + b)
    return a + b

if __name__ == '__main__':
    add(1, 2)
```

执行结果：
``` python
haha warp
func add: 3
result: 3
```
解释：

- add会作为参数func传给get_result函数,然后get_result返回warp函数。
- 执行add.__name__会输出wrap
- 其实这是python解释器在背后吧warp赋值给了add，然后add就保存了warp的索引，每次调用add(a, b)都是在执行warp(a, b)
## 解决name属性被修改的问题
``` python
from functools import wraps

def get_result(func):
    @wraps(func)
    def warp(*args, **kwargs):
        print('haha warp')
        result = func(*args, **kwargs)
        print('result:', result)
        return result
    return warp

@get_result
def add(a, b):
print('func add:', a + b)
return a + b

if __name__ == '__main__':
a = add
a(1, 2)
print('name:', a.__name__)

if __name__ == '__main__':
a = add
a(1, 2)
print(a.__name__)
```
释执行结果：
``` python
haha warp
func add: 3
result: 3
name: add
```

解释：
- functools.warps是为了在装饰器中方便的拷贝被装饰函数的签名


## 怎么生成传参装饰器
``` python
from functools import wraps



def user_auth(has_token=True):
 def decorator(func):
    @wraps(func)
    def warp(self, data, *args, **kwargs):
        if has_token:
            token = data.get('token', False)
            if not token:
                return {}
            else:
                uid = data.get('uid', False)
                passwd = data.get('passwd', False)
                if (bool(uid) and bool(passwd)) is False:
                    return {}
            return func(self, data, *args, **kwargs)
    return warp
 return decorator

class UserInfo(object):

@user_auth(has_token=True)
def get_info(self, data):
        # data: 调用方传递的用户的凭证信息包括token
info = {'age': 18}
return info


@user_auth(has_token=False)
def login(self, data):
        data: 登录信息
info = {'age': 18}
return info

if __name__ == '__main__':
user_info = UserInfo()

print('-------------login------------')
data = {'uid': 'uid1', 'passwd': 'sss'}
print('has uid passwd:', user_info.login(data))
print('no uid passwd:', user_info.login({}))
print('-------------get_info------------')
data = {'token': 'token'}
print('has token:', user_info.get_info(data))
print('no token:', user_info.get_info({}))
```
执行结果及解释
结果：
``` python
# -------------login------------
has uid passwd: {'age': 18}
no uid passwd: {}
# -------------get_info------------
has token: {'age': 18}
no token: {}
```
解释：

    这个用户鉴权装饰器应对于登陆和登陆后的鉴权,如参数has_token,当为False时,说明
    是登陆,反之说明已经登陆成功,应携带token参数请求接口,其他的逻辑应该很简单.