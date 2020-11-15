---
title: Web框架本质-Django
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---


## Web框架本质


web系统概念

``` python

1. Http，无状态，短连接 
2. 浏览器（socket客户端）、网站（socket服务端）

```


web框架本质

``` python
import socket 

def handle_request(client): 
buf = client.recv(1024) 
client.send("HTTP/1.1 200 OK\r\n\r\n") 
client.send("Hello, Seven") 
def main(): 
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
sock.bind(('localhost',8000)) 
sock.listen(5) while True: 
connection, address = sock.accept() 
handle_request(connection) 
connection.close() if __name__ == '__main__': main()

```


自定义Web框架

``` python

a. socket服务端 
b. 根据URL不同返回不同的内容 路由系统： URL -> 函数 
c. 字符串返回给用户 模板引擎渲染： HTML充当模板（特殊字符） 自己创造任意数据 字符串

```
静态网站处理方式：
``` python

import socket 

def f1(request): 

    # #  处理用户请求，并返回相应的内容 :param request: 用户请求的所有信息 :return:  
    f = open('index.fsw','rb') 
    data = f.read() 
    f.close() 
    return data 

def f2(request): 
    f = open('aricle.tpl','rb') 
    data = f.read() 
    f.close() 
    return data 

routers = [ 
    ('/xxx', f1), 
    ('/ooo', f2), 
] 

def run(): 
    sock = socket.socket() 
    sock.bind(('127.0.0.1',8080)) 
    sock.listen(5) 
    while True: 
        conn,addr = sock.accept() # hang住 # 有人来连接了 # 获取用户发送的数据 
        data = conn.recv(8096) 
        data = str(data,encoding='utf-8') 
        headers,bodys = data.split('\r\n\r\n') 
        temp_list = headers.split('\r\n') 
        method,url,protocal = temp_list[0].split(' ') 
        conn.send(b"HTTP/1.1 200 OK\r\n\r\n") 
        func_name = None for item in routers: 
        if item[0] == url: 
            func_name = item[1] 
            break 
        if func_name: 
            response = func_name(data) 
        else: 
            response = b"404" 
        conn.send(response) 
        conn.close() 
    
if __name__ == '__main__': run()

```
动态网站处理方式一（手动进行替换的模版引擎）：
``` python

import socket
def f3(request): 
    import pymysql # 创建连接,获得数据 
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123',db='db666') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
    cursor.execute('select id,username,password from userinfo') 
    user_list = cursor.fetchall() 
    cursor.close() 
    conn.close() # 组装数据模型 
    content_list=[] 
    for row in user_list: 
        tp = '<tr><td>%s</td><td>%s</td><td>%s</td></tr>'%(row['id'],row['username'],row['password']) 
        content_list.append(tp) 
        content = "".join(content_list) # 将列表中的数据拼接成字符串 # 模板渲染（模板+数据） 
    f = open('userlist.html','r',encoding='utf-8') 
    template = f.read() 
    f.close() 
    data = template.replace('@@sdfsdffd@@', content) 
    return bytes(data, encoding='utf-8') # 路由系统 

routers = [ ('/userlist.htm', f3), ] 

def run(): 
    sock = socket.socket() 
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1) 
    sock.bind(('127.0.0.1',8080)) 
    sock.listen(5) 
    while True: 
        conn,addr = sock.accept() # hang住 # 有人来连接了 # 获取用户发送的数据 
        data = conn.recv(8096) 
        data = str(data,encoding='utf-8') 
        headers,bodys = data.split('\r\n\r\n') 
        temp_list = headers.split('\r\n') 
        method,url,protocal = temp_list[0].split(' ') 
        conn.send(b"HTTP/1.1 200 OK\r\n\r\n") 
        func_name = None 
        for item in routers: 
            if item[0] == url: 
                func_name = item[1] 
                break 
            if func_name: 
                response = func_name(data) 
            else: 
                response = b"404" 

        conn.send(response) 
        conn.close() 

if __name__ == '__main__': run()

```

Django项目前期配置

``` code

pip3 install django # 创建Django程序 
django-admin startproject mysite # 进入程序目录 
cd mysite # 启动socket服务端，等待用户发送请求 
python manage.py runserver 127.0.0.1:8080

```

Django配置文件：

settings.py 

…… 

DIRS': [os.path.join(BASE_DIR, 'template')], 

……

``` python

Mysql数据库： 
DATABASES = { 
    'default': { 
        'ENGINE': 'django.db.backends.mysql', 
        'NAME':'dbname', 
        'USER': 'root', 
        'PASSWORD': 'xxx',
        'HOST': '', 
        'PORT': '', 
    } 
} 
#备注： 
# 由于Django内部连接MySQL时使用的是MySQLdb模块，而python3中还无此模块，所以需要使用pymysql来代替 
# 如下设置放置的与project同名的配置的 __init__.py文件中 
import pymysql 
pymysql.install_as_MySQLdb()　

```
静态文件路径： static目录 
``` python

STATIC_URL = '/static/' 
STATICFILES_DIRS = ( os.path.join(BASE_DIR,'static'), )

```
额外配置（跨站请求伪装crsf）: 
``` python

MIDDLEWARE = [
    …… 
    #'django.middleware.csrf.CsrfViewMiddleware', 
    …… 
    ]

```


项目案例


网站请求
``` python

# urls.py 
urlpatterns = [ 
    # url(r'^admin/', admin.site.urls), 
    url(r'^index/',index), 
] def index(request): 
# return HttpResponse('Index') 
return render(request, 'index.html',{ 
    'name': 'tom', 
    'users':['李志','李杰'], 
    'user_dict':{
        'k1':'v1', 
        'k2':'v2'
    }, 
    'user_list_dict':[ {
        'id':1,'name':
        'tom',
        'email':'tom@1231.com'
    }, {
        'id':1,
        'name':'tom',
        'email':'tom@1231.com'
    }, {
        'id':1,
        'name':'tom',
        'email':'tom@1231.com'
    }, 
] 
})

```

网站登陆
``` python

# urls from django.conf.urls 
import url from django.contrib 
import admin from django.shortcuts 
import HttpResponse,render,redirect 

urlpatterns = [ 
    # url(r'^admin/', admin.site.urls), 
    url(r'^login/',login), 
] 
def login(request): 
    # #  处理用户请求，并返回内容 :param request: 用户请求相关的所有信息（对象） :return:  # 字符串 # 
    return HttpResponse('<input type="text" />') 
    # return HttpResponse('login.html') 
    # 自动找到模板路径下的login.html文件，读取内容并返回给用户 
    # 模板路径的配置 print(request.GET) 
    # 结果为字典格式，值为列表类型 if request.method == "GET": return render(request,'login.html') else: 
    # 用户POST提交的数据（请求体） u = request.POST.get('user') p = request.POST.get('pwd') if u == 'root' and p == '123': 
    # 登录成功 
    # return redirect('http://www.oldboyedu.com') return redirect('/index/') 
    # 重定向 else: 
    # 登录失败 return render(request,'login.html',{'msg': '用户名或密码错误'})

```
<h2 id="a2">
<a id="django_473"></a>django学员管理系统</h2>


数据库设计结构

``` python

表结构：班级\学生\老师 （班级表）： 
id          title 
1           全4期 
2           全5期 
学生表）： 
id          name        班级ID（FK） 
1           张杰            1 
老师表）： 
id          name 
1           林峰 
2           林狗 
3           苑天 
老师班级关系表）： 
id          老师ID         班级ID 
1           1               1 
2           1               2 
3           2               2

```


班级管理模块


查(母版继承)
``` python

# urls 
urlpatterns = [ 
    # url(r'^admin/', admin.site.urls), 
    url(r'classes/', views.classes), 
]

```
``` python

# view from django.shortcuts 
import render,redirect 
import pymysql 
def classes(request): 
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', password='123', db='s4db65', charset='utf8') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
    # 设置查询结果为字典格式 
    cursor.execute("select id, title from class") 
    class_list = cursor.fetchall() 
    # 结果为字典 
    cursor.close() 
    conn.close() 
    print(class_list) 

    return render(request, 'classes.html',{'class_list':class_list})

```

增
``` python

# urls 
urlpatterns = [ 
    # url(r'^admin/', admin.site.urls), 
    url(r'^add_class/', views.add_class), 
]

```
``` python

# view 
def add_class(request): 
    if request.method == 'GET': 
        return render(request,'add_class.html') 
    else: 
        print(request.POST) 
        v = request.POST.get('title') 
        conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd='123',db='s4db65',charset='utf8') 
        cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
        cursor.execute('insert into class(title) value(%s)', [v,]) 
        conn.commit() 
        # 提交事务 
        cursor.close() 
        conn.close() 
        return redirect('/classes/')

```

删
``` python

# urls 
urlpatterns = [ 
    url(r'^del_class/', views.del_class),
]

```
``` python

# view 
def del_class(request): 
    nid = request.GET.get('nid') 
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='s4db65', charset='utf8') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
    cursor.execute('delete from class where id=%s',[nid,]) 
    conn.commit() 
    cursor.close() 
    conn.close() 
    return redirect('/classes/')

```

改
``` python

# urls 
urlpatterns = [ url(r'^edit_class/', views.edit_class), ]

```
``` python

# view 
def edit_class(request): 
    if request.method == 'GET': 
        # 获取数据 
        nid = request.GET.get('nid') 
        conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd = '123', db='s4db65',charset='utf8') 
        cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
        cursor.execute('select id, title from class where id=%s', [nid,]) 
        result = cursor.fetchone() 
        cursor.close() 
        conn.close() 
        print(result) 
        return render(request,'edit_class.html',{'result':result}) 
    else: 
        nid = request.GET.get('nid') 
        title = request.POST.get('title') 
        print(nid, title) 
        conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd='123',db='s4db65', charset='utf8') 
        cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
        cursor.execute('update class set title=%s where id=%s',[title,nid,]) conn.commit() cursor.close() conn.close() 
        return redirect('/classes/')

```

模态对话框实现增加班级功能
``` python

# urls 
urlpatterns = [url(r'^modal_add_class/', views.modal_add_class]

```
``` python

# views sqlheper工具类（将增删改查进行封装）： 
def modify(sql,args): 
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='s4db65', charset='utf8') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) cursor.execute(sql,args) 
    conn.commit() 
    cursor.close() 
    conn.close() 
    ---------- 
def modal_add_class(request): 
    title = request.POST.get('title') 
    if len(title) > 0: 
        sqlheper.modify('insert into class (title) value(%s)',[title,]) 
        return HttpResponse('ok') 
    else: 
        return HttpResponse('班级标题不能为空')

```


学生管理模块


查
``` python

# urls 
url(r'^students/',views.students),

```
``` python

# views 
def students(request): 
    conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd='123',db='s4db65',charset='utf8') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
    cursor.execute('select student.id,student.name,class.title from student left JOIN class on student.class_id = class.id') 
    student_list = cursor.fetchall() 
    cursor.close() 
    conn.close() 
    print('结果：',student_list) 
    return render(request,'students.html',{'student_list':student_list})

```

增
``` python

# urls 
url(r'^add_student/',views.add_student)

```
``` python

# views 
def add_student(request): 
    if request.method == 'GET': 
        #只负责跳转页面 
        conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd='123',db='s4db65',charset='utf8') 
        cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
        cursor.execute('select id,title from class') 
        class_list = cursor.fetchall() 
        cursor.close() 
        conn.close() 
        return render(request,'add_student.html',{'class_list':class_list}) 
    else: 
        name = request.POST.get('name')
        #负责提交数据的跳转页面 
        class_id = request.POST.get('class_id') 
        conn = pymysql.connect(host='127.0.0.1',port=3306,user='root',passwd='123',db='s4db65',charset='utf8') 
        cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
        cursor.execute('insert into student(name,class_id)values(%s,%s)',[name,class_id,]) 
        conn.commit() 
        cursor.close() 
        conn.close() 
        return redirect('/students/')

```

改
``` python

# urls 
url(r'^edit_student/',views.edit_student),

```
``` python

# views 
from utils import sqlheper 
def edit_student(request): 
    if request.method == "GET": 
        nid = request.GET.get('nid') 
        class_list = sqlheper.get_list("select id,title from class",[]) 
        current_student_info = sqlheper.get_one('select id,name,class_id from student where id=%s',[nid,]) 
        print('结果：',class_list,current_student_info,nid) 
        return render(request,'edit_student.html',{'class_list': class_list,'current_student_info':current_student_info}) 
    else: 
        nid = request.GET.get('nid') 
        name = request.POST.get('name') 
        class_id = request.POST.get('class_id') 
        sqlheper.modify('update student set name=%s,class_id=%s where id=%s',[name,class_id,nid,]) 
        return redirect('/students/')

```

模态对话框实现增
``` python

# urls 
url(r'^modal_add_student/', views.modal_add_student),

```
``` python

# views 
def modal_add_student(request): 
    ret = {'status':True,'message':None} 
    try: 
        name = request.POST.get('name')
        print('result',name) 
        class_id = request.POST.get('class_id') 
        sqlheper.modify('insert into student (name,class_id)values(%s,%s)',[name,class_id,]) 
    except Exception as e: 
        ret['status']=False 
        ret['message']=str[e] 
        return HttpResponse(json.dumps(ret))

```

模态对话框实现改
``` python

# urls 
url(r'^modal_edit_student/', views.modal_edit_student),

```
``` python

# views 
def modal_edit_student(request): 
    ret = {'status': True,'message': None} 
    try: 
        nid = request.POST.get('nid') 
        name = request.POST.get('name') 
        class_id = request.POST.get('class_id') 
        sqlheper.modify('update student set name=%s,class_id=%s where id=%s',[name,class_id,nid,]) 
    except Exception as e: 
        ret['status'] = False 
        ret['message'] = str(e) 
        return HttpResponse(json.dumps(ret))

```


老师管理模块


查
``` python

# urls 
url(r'^teachers/', views.teachers),

```
``` python

# views 
# 多对多，以老师表展示 
def teachers(request): 
    teacher_list = sqlheper.get_list('select id,name from teacher',[]) 
    teacher_list = sqlheper.get_list(
        #  select teacher.id as tid,teacher.name,class.title from teacher 
                LEFT JOIN teacher2class on teacher.id = teacher2class.teacher_id 
                # left JOIN class on class.id = teacher2class.class_id; ,[]) 
    print(teacher_list) 
    result = {} 
    for row in teacher_list: 
        tid =row['tid'] 
        if tid in result: 
            result[tid]['titles'].append(row['title']) 
        lse: 
            result[tid] = {'tid': row['tid'],'name':row['name'],'titles': [row['title'],]} 
            
    return render(request,'teacher.html',{'teacher_list':result.values()})

```

增
``` python

# ursl 
url(r'^add_teacher/', views.add_teacher),

```
``` python

# views 
def add_teacher(request): 
    if request.method == "GET": 
        class_list = sqlheper.get_list('select id,title from class',[]) 
        return render(request,'add_teacher.html',{'class_list': class_list}) 
    else: 
        name = request.POST.get('name') 
        # 老师表中添加一条数据 
        teacher_id = sqlheper.create('insert into teacher(name) values(%s)',[name,]) 
        # 老师和班级关系表中插入数据 
        class_ids = request.POST.getlist('class_ids') 
        # 一次连接，一次提交 data_list = [] 
        for cls_id in class_ids: 
            temp = (teacher_id,cls_id,) 
            data_list.append(temp) 
            obj = sqlheper.SqlHelper() 
            # 通过自定义的mysql组件获得mysql连接对象 
            obj.multiple_modify('insert into teacher2class(teacher_id,class_id) values(%s,%s)',data_list) obj.close() 
            
        return redirect('/teachers/')

```

改
``` python

# urls 
url(r'^edit_teacher/',views.edit_teacher),

```
``` python

# views 
def edit_teacher(request): 
    if request.method == "GET": 
        nid = request.GET.get('nid') 
        obj = sqlheper.SqlHelper() 
        teacher_info = obj.get_one('select id,name from teacher where id =%s',[nid,]) 
        class_id_list = obj.get_list('select class_id from teacher2class where teacher_id=%s',[nid,]) 
        class_list = obj.get_list('select id,title from class',[]) 
        obj.close() 
        print('当前老师信息',teacher_info) 
        print('当前老师任教的班级id',class_id_list) 
        temp = [] 
        for i in class_id_list: 
            temp.append(i['class_id']) 
            print('所有班级',class_list) 
            # return HttpResponse('...') 
            return render(request,'edit_teacher.html',{ 'teacher_info': teacher_info, 'class_id_list': temp, 'class_list': class_list, }) 
        else: 
            nid = request.GET.get('nid') 
            name = request.POST.get('name') 
            class_ids = request.POST.getlist('class_ids') 
            obj = sqlheper.SqlHelper() 
            # 更新老师表 
            obj.modify('update teacher set name=%s where id=%s',[name,nid]) 
            # 更新老师和班级关系表 
            # 先把当前老师和班级的对应关系删除，然后再添加 
            obj.modify('delete from teacher2class where teacher_id=%s',[nid,]) 
            data_list = [] 
            for cls_id in class_ids: 
                temp = (nid,cls_id,) 
                data_list.append(temp) 
                obj = sqlheper.SqlHelper() 
                obj.multiple_modify('insert into teacher2class(teacher_id,class_id) values(%s,%s)',data_list) 
                
                obj.close() 
            
            return redirect('/teachers/')

```

模态对话框增
``` python

# urls 
url(r'^get_all_class/', views.get_all_class), 
url(r'^modal_add_teacher/', views.modal_add_teacher),

```
``` python

# views 
def get_all_class(request): 
    obj = sqlheper.SqlHelper() 
    class_list = obj.get_list('select id,title from class',[]) 
    return HttpResponse(json.dumps(class_list)) 

def modal_add_teacher(request): 
    ret = {'status': True,'message': None} 
    try: 
        name = request.POST.get('name') 
        class_id_list = request.POST.getlist('class_id_list') 
        teacher_id = sqlheper.create('insert into teacher(name) values(%s)',[name,]) 
        data_list = [] 
        for cls_id in class_id_list: 
            temp = (teacher_id,cls_id,) 
            data_list.append(temp) 
            obj = sqlheper.SqlHelper() 
            obj.multiple_modify('insert into teacher2class(teacher_id,class_id) values(%s,%s)',data_list) 
            obj.close() 
    except Exception as e: 
        ret['status'] = False 
        ret['message'] = "处理失败" 
        return HttpResponse(json.dumps(ret))

```
``` python

# sqlheper 
def create(sql,args): 
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='', db='s4db65', charset='utf8') 
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
    cursor.execute(sql,args) 
    conn.commit() 
    last_row_id = cursor.lastrowid 
    cursor.close() 
    conn.close() 
    return last_row_id 

class SqlHelper(object): 
    def __init__(self): 
        self.connect() 
        # 读取配置文件 
        def connect(self): 
            self.conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='', db='s4db65', charset='utf8') 
            self.cursor = self.conn.cursor(cursor=pymysql.cursors.DictCursor) 

        def multiple_modify(self,sql,args): 
            self.cursor.executemany(sql,args) 
            self.conn.commit() 
            
        def close(self): 
            self.cursor.close() 
            self.conn.close()

```



用户登陆(包含cookies内容)

``` python

# urls 
url(r'^login/', views.login),

```
``` python

# views 
def login(request): 
    if request.method == "GET": 
        return render(request,'login.html') 
    else: 
        user = request.POST.get('username') 
        pwd = request.POST.get('password') 
        if user == 'tom' and pwd == '123': 
            obj = redirect('/classes/') 
            obj.set_signed_cookie('ticket',"567",salt='jjjjjj',max_age=900,path='/') 
            # 设置cookies，浏览器的cookies中会直接以明文显示567 
            return obj 
        else: 
        return render(request,'login.html') 
def classes(request): 
    # 去请求的cookie中找凭证 
    # tk = request.COOKIES.get('ticket') 
    tk = request.get_signed_cookie('ticket',salt='jjjjjj') 
    print(tk) 
    if not tk: 
    return redirect('/login/') 

conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='', db='s4db65', charset='utf8') 
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor) 
cursor.execute("select id,title from class") 
class_list = cursor.fetchall() 
cursor.close() 
conn.close() 
return render(request, 'classes.html', {'class_list': class_list})

```
``` python

# 内容延伸 装饰器装饰views中的函数 @xzxx 
def index(request): 
    obj = HttpResponse('...') 
    obj.set_cookie(.....) 
    request.COOKIES.get(...) 
    obj.set_signed_cookie(.....) 
    request.get_signed_cookie(....)

```
<h2 id="a3">
<a id="_1707"></a>动态路由</h2>


urls

``` python

# 动态路由 
# url(r'^edit/(\w+)/(\w+)/', views.edit), 
# url(r'^edit/(?P<a1>\w+)/(?P<a2>\w+)/', views.edit), 
# url(r'^edit/(\w+).html$', views.edit) 
#正则表达式实现静态伪装

```


views

``` python

def edit(request,*args,**kwargs):
    #获得请求数据 
    print(args,kwargs) 
    return HttpResponse('...')

```
>路由分发
``` python

＃路由分发实现不同模块分开编辑，团队协作开发 
url(r'^app01/', include('app01.urls')),　
#不同的程序模块 
url(r'^app02/', include('app02.urls')), 
# url(r'^', default), 
# 设置默认页面，接收任意请求 
# url(r'^', views.index), #

```
反向生成URL（Django独有）


通过别名反射成URL，简化URL的填写， 用于权限管理

``` python

#urls 
url(r'^edit/(\w+)/(\w+)/', views.edit,name='n2'),

```
ORM基本操作（数据表+数据行操作


操作前的配置步骤

``` python

__init__.py文件 (ORM默认使用SQLlite连接数据库,需改成Mysql) 
添加： 
import pymysql 
pymysql.install_as_MySQLdb()

```
在mysql中手动创建数据库
``` python

# 配置setting文件中重写数数据库配置 
TEMPLATES = [
…… 
'DIRS': [os.path.join(BASE_DIR,'templates')],
…… 
] 
DATABASES = { 
    'default': { 
        'ENGINE': 'django.db.backends.mysql', 
        'NAME':'s4day70db', 
        'USER': 'root', 
        'PASSWORD': '', 
        'HOST': 'localhost', 
        'PORT': 3306, 
    } 
}

```


操作表


创建表
``` python

# models 
from django.db import models 
class UserInfo(models.Model):
    # 在数据库中的表名为模块名_UserInfo 
    nid = models.BigAutoField(primary_key=True) 
    #可不写，默认会生成表中的id字段 
    username = models.CharField(max_length=32) 
    password = models.CharField(max_length=64)

```
``` python

# settings配置文件中安装app01模块 I
NSTALLED_APPS = [ 
    …… 
    'app01', 
]

```
``` python

# 执行语句 
#生成数据表配置文件，包含生成及修改等信息 
python manage.py makemigrations 

#执行生成的配置文件
python manage.py migrate 

```

修改表：
``` python

# models 
class UserInfo(models.Model): 
    …… 
    age = models.IntegerField(default=1) 
    # 新增字段时需设置默认值/允许为空 
    # age = models.IntegerField(null=True)

```
``` python

# 执行语句：
#生成数据表配置文件，包含生成及修改等信息 
python manage.py makemigrations 

#执行生成的配置文件
python manage.py migrate 

```

删除表：
``` python
# 参考修改表

```

创建外键关联：
``` python

# models 
class UserGroup(models.Model): 
    #等同于主表，需将该类写在子表前面 
    title = models.CharField(max_length=32) 

class UserInfo(models.Model): 
    # 外键所在的表等同于子表 
    …… 
    ug = models.ForeignKey('UserGroup',null=True,on_delete='') 
    # 外键关联字段关联UserGroup表中的Id字段，ug在UserInfo数据表中的字段为ug_id

```

操作单表数据行：
``` python

# 增 
from app01 import models 
models.UserGroup.objects.create(title='销售部')

```
``` python

# 删 
models.UserGroup.objects.filter(id=2).delete()

```
``` python

# 改 
models.UserGroup.objects.filter(id=2).update(title='公关部')

```
``` python

# 查 
group_list = models.UserGroup.objects.all() 
group_list = models.UserGroup.objects.filter(id=1) 

group_list = models.UserGroup.objects.filter(id__gt=1) #大于1 
group_list = models.UserGroup.objects.filter(id__lt=1) #小于1

```
<h2 id="a7">
<a id="DjangoCBV_1907"></a>Django视图CBV</h2>


urls

``` python
url(r'^login.html$', views.Login.as_view),

```


views

``` python

from django.views import View 
class Login(View): 
    # 继承View类作为父类 # 
    #重写父类方法，该方法可作为装饰器功能 
    def dispatch(self, request, *args, **kwargs): 
        # 自定制 dispatch方法，除了可以利用父类中原dispatch方法，还可以自定制处理逻辑 
        print('before') 
        obj = super(Login,self).dispatch(request, *args, **kwargs) 
        # 传入Login对象作为参数，调用父类中的方法 
        print('after') 
        return obj 
        
    def get(self,request): 
        # 请求为get请求时，自动调用该方法 
        # return HttpResponse('Login.get') 
        return render(request,'login.html') 
        
    def post(self,request):
        # 请求是POST请求时，自动调用该方法 
        print(request.POST.get('user')) 
        return HttpResponse('Login.post')

```
<h2 id="a8">
<a id="ORM_1948"></a>ORM连表操作（一对多）（一对多中，外键存在于从表中）</h2>


models

``` python

from django.db import models 

class UserType(models.Model): 
    # 用户类型 
    title = models.CharField(max_length=32) 
    
class UserInfo(models.Model): 
     用户表 
    name = models.CharField(max_length=16) 
    age = models.IntegerField() 
    ut = models.ForeignKey('UserType',on_delete='') 
    # 关联UserType表中的id字段

```


urls

``` python
url(r'^test.html$', views.test),

```


views

``` python

# 创建数据 
# models.UserType.objects.create(title='普通用户') 
# models.UserType.objects.create(title='二逼用户') 
# models.UserType.objects.create(title='牛逼用户')
# models.UserInfo.objects.create(name='方少伟',age=18,ut_id=1) 
# models.UserInfo.objects.create(name='由秦兵',age=18,ut_id=2) 
# models.UserInfo.objects.create(name='刘庚',age=18,ut_id=2) 
# models.UserInfo.objects.create(name='陈涛',age=18,ut_id=3) 
# models.UserInfo.objects.create(name='王者',age=18,ut_id=3) 
# models.UserInfo.objects.create(name='杨涵',age=18,ut_id=1)

```

一对多的正向操作（让存在外键的从表进行跨表，去查询关联主表中的字段）：
``` python

# 获得Queryset对象格式数据（格式：对象名.外键字段名.关联表字段名） 
obj = models.UserInfo.objects.all().first() 
# 获取一条数据，无需再obj[0]来获取具体的对象 
print(obj.name,obj.age,obj.ut.title) 
# 获取跨表后的字段用obj.ut.title方式，ut是外键关联中的字段

```
``` python

# 获得查询结果为字典格式组合成列表类型的Queryset数据,可用list方法转换成列表格式（从表中的外键字段名__主表中的字段名） 
v1 = models.UserInfo.objects.values('id','name','ut__title')

```
``` python

# 获得查询结果为元组格式组合成列表类型的Queryset数据,可用List转换成列表（从表中的外键字段名__主表中的字段名） 
result = models.UserInfo.objects.all().values_list('id','name'.'ut__title')

```

一对多的反向操作(让主表进行跨表，去查询（有外键关联字段）从表中相应的字段作为查询条件或查询结果)：
``` python

# 获得Queryset对象格式的数据,(格式：主表的QuerySet对象名.从表名__set.all()) （一个用户类型下可以有很多用户，获得所有用户类型对应的用户信息数据）： 
obj = models.UserType.objects.all().first() 
for row in obj.userinfo_set.all(): 
    print(row.name,row.age)

```
``` python

# 获得字典格式Queryset对象格式的数据,需要转换成json字符串格式通过ajax返回数据时，可用list方法转换成列表（从表表名小写__主表字段名） 
v2 = models.UserType.objects.values('id','title','userinfo__name')

```
``` python

# 获得元组格式Queryset对象格式的数据,需要转换成json字符串格式通过ajax返回数据时，可用list方法转换成列表。（外键字段名__从表字段名） 
result = models.UserType.objects.all().values_list('id','name','userinfo__name')

```

一对多其他参考：
``` python

# 跨表 
# 正向查询： 
1. 
    q = UserInfo.objects.all().first() 
    q.ug.title 
2. 
    UserInfo.objects.values('nid','ug_id') 
    UserInfo.objects.values('nid','ug_id','ug__title') 
3. 
    UserInfo.objects.values_list('nid','ug_id','ug__title') 

# 反向查询： 
1. 小写的表名_set 
    obj = UserGroup.objects.all().first() 
    result = obj.userinfo_set.all() [userinfo对象,userinfo对象,] 
2. 小写的表名 
    v = UserGroup.objects.values('id','title') 
    v = UserGroup.objects.values('id','title','小写的从表名称') 
    v = UserGroup.objects.values('id','title','小写的从表名称__age') 
3. 小写的表名 
    v = UserGroup.objects.values_list('id','title') 
    v = UserGroup.objects.values_list('id','title','小写的表名称') 
    v = UserGroup.objects.values_list('id','title','小写的表名称__age')

```
<h2 id="a9">
<a id="ORM_2066"></a>ORM连表操作（多对多）</h2>

（多对多中，主表与从表的外键都共同存在于第3张关联表中） ##


手动创建第三张关联表（推荐，手动更灵活）

``` python

# models 
from django.db import models 
class Boy(models.Model): 
    name = models.CharField(max_length=32) 
    
class Girl(models.Model): 
    nick = models.CharField(max_length=32) 
    
class Love(models.Model): 
    b = models.ForeignKey('Boy',on_delete='') 
    g = models.ForeignKey('Girl',on_delete='') 
    
    class Meta: 
        #添加联合唯一字段 
        unique_together = [ ('b','g'), ]

```
``` python

# views 
# 添加数据 
objs = [ 
    models.Boy(name='方少伟'), 
    models.Boy(name='由秦兵'), 
    models.Boy(name='陈涛'), 
    models.Boy(name='闫龙'), 
    models.Boy(name='吴彦祖'), 
] 
models.Boy.objects.bulk_create(objs,5) 
# 批量添加数据 
objss = [ 
    models.Girl(nick='小鱼'), 
    models.Girl(nick='小周'), 
    models.Girl(nick='小猫'), 
    models.Girl(nick='小狗'), 
] 
models.Girl.objects.bulk_create(objss,5) 
# 1. 查询和方少伟有关系的姑娘 
# 第一种查询方式：跨表反向查询，获得对象 
# obj = models.Boy.objects.filter(name='方少伟').first() 
# love_list = obj.love_set.all() 
# 反向查询，获得所有love表中的对象 
# for row in love_list: 
# print(row.g.nick) 
# 获得对应的姑娘数据 
# 第二种查询方式：连表查询（正向查询），直接查询love表，获得对象 
# love_list = models.Love.objects.filter(b__name='方少伟') 
#获得Queryset对象 
# for row in love_list: 
# print(row.g.nick) 
# （推荐）第三种查询方式：只发送一次sql连接请求，获得字典,属于正向查询 
# love_list = models.Love.objects.filter(b__name='方少伟').values('g__nick') # for item in love_list: 
# 获得字典格式的列表，[{'g__nick':'xxx},] # print(item['g__nick']) 
# （推荐）第四种查询方式：相当于inner join方式连表查询，只发送一次sql连接请求，获得对象 
# love_list = models.Love.objects.filter(b__name='方少伟').select_related('g') 
# for obj in love_list: 
# 获得对象 
# print(obj.g.nick) 
# 总结：多对多都是先获得关联表中的数据，再进行跨表操作 
# 对象格式用.（点）进行跨表 字典和元组采用_（双下划线）进行跨表

```


Django自动生成第三张关联表（无法再手动添加其它字段）

``` python

# models 
class Boy(models.Model): 
    name = models.CharField(max_length=32) 
    m = models.ManyToManyField('Girl') 
#自动生成关联表m，以两张表中的id字段作为关联字段 
class Girl(models.Model): 
    nick = models.CharField(max_length=32)

```
``` python

# views 
# 增 
# obj = models.Boy.objects.filter(name='方少伟').first() 
# obj.m.add(2) 
# obj.m.add(2,4) 
# 创建关联表的多条数据 
# obj.m.add(*[1,3]) 
# 以列表形式创建多条数据 
# 删 
# obj.m.remove(1) 
# obj.m.remove(2,3) 
# 删除关联表的多条数据 
# obj.m.remove(*[4,]) 
# obj.m.set([1,]) 
# 覆盖数据库所有数据,重置 
# obj = models.Boy.objects.filter(name='方少伟').first() 
# obj.m.clear() 
# 删除所有与方少伟的关联数据 
# 查 
# 正向查询出单条数据 
# obj = models.Boy.objects.filter(name='方少伟').first() 
# 
# girl_list = obj.m.all() 
# girl_list = obj.m.filter(nick='小鱼') 
# 相当于从从表跨表到主表查询 
# print(girl_list) 
# 反向查询出多条数据 
# obj = models.Girl.objects.filter(nick='小鱼').first() 
# print(obj.id,obj.nick) 
# boy_list = obj.boy_set.all() 
# 相当于从主表跨表到从表反向查询

```


手动与自动结合

``` python

# models 
class Boy(models.Model): 
    name = models.CharField(max_length=32) 
    m = models.ManyToManyField(to='Girl',through="Love",through_fields=('b','g',)) 
# 可不写to关键字 
class Girl(models.Model): 
    nick = models.CharField(max_length=32) 
    
class Love(models.Model): 
    b = models.ForeignKey('Boy',on_delete='') 
    g = models.ForeignKey('Girl',on_delete='') 
    
class Meta: unique_together = [ ('b','g'), ]

```
``` python

# views 
obj = models.Boy.objects.filter(name='方少伟').first() 
obj.m.add(1) 
# obj.m.remove(1) 
# obj.m.clear() 可以 v = obj.m.all() print(v)

```
<h2 id="a10">
<a id="ORM_2219"></a>ORM连表操作（多对多自关联）</h2>

原理：等同于复制出一张新表


models

``` python

class UserInfo(models.Model): 
    nickname = models.CharField(max_length=32) 
    username = models.CharField(max_length=32) 
    password = models.CharField(max_length=64) 
    gender_choices = ( (1,'男'), (2,'女'), ) 
    gender = models.IntegerField(choices=gender_choices) 
    m = models.ManyToManyField('UserInfo') 
# 多对多自关联字段，自动生成第二张表，字段分别为from_userinfo_id和to_userinfo_id; 
# 表中的m属性不会在userinfo表中生成m字段

```
``` python

def test(request): 
# 查男生（通过m字段查询属于正向操作） 
xz = models.UserInfo.objects.filter(id=1).first() 
#id为1代表男生的1条数据 
u = xz.m.all() for row in u: print(row.nickname) 
# 查女生(通过表名称_set查询属于反向操作) 
xz = models.UserInfo.objects.filter(id=4).first() 
#id为4代表女生的1条数据 
v = xz.userinfo_set.all() 
for row in v: 
    print(row.nickname) 
    
return HttpResponse('...')

```


外键自关联（常用于评论表功能）


等同于复制出一张新表，用原表中的外键作连表操作
``` python

class Comment(models.Model): 
    # #  评论表  
    # 新闻ID 
    news_id = models.IntegerField() 
    # 评论内容 
    content = models.CharField(max_length=32) 
    # 评论者 
    user = models.CharField(max_length=32) 
    #related_name表示反向查询时，代替 表名_set 和 表名__字段名 
    reply = models.ForeignKey('Comment',null=True,blank=True,related_name='xxxx') 
    #  
    新闻ID                        reply_id 
    1           别比比 root         null 
    1           就比比 root         null 
    1           瞎比比 shaowei      null 
    2           写的正好 root       null 
    1           拉倒吧 由清滨         2 
    1           拉倒吧1 xxxxx        2 
    1           拉倒吧2 xxxxx       5 
    #  
        
    #  
    新闻1 
        别比比 
        就比比 
            - 拉倒吧 
            - 拉倒吧2 
        - 拉倒吧1 
        瞎比比 
        
    新闻2： 
        写的正好 
    # 

```
<h2 id="a11">
<a id="ORMmodels_2294"></a>ORM操作补充（models模块中数据表属性定义操作）</h2>


models

``` python

from django.db import models 
from django.core.validators import RegexValidator 
class UserAdmin(models.Model): 
    username = models.CharField(max_length=32) 
    email = models.EmailField(
        ull=True,
        default='111', 
        db_index=True,
        unique=True, 
        blank=True,
        verbose_name='邮箱', 
        editable=True, 
        help_text='字段提示信息的内容', 
    ) 
# blank控制admin是否为空， 
file = models.FileField() 
#文件字段，只针对admin 
ctime = models.DateTimeField() 
# 日期字段 
# 自定义正则表达式验证规则 
test = models.CharField(
    max_length=32, 
    error_messages={ 'c1': '优先错信息1', }, 
    validators=[RegexValidator(regex='root_\d+', message='错误了', code='c1')], 
    null=True 
) 
# 数字及小数 
# num = models.FloatField() 
# num = models.IntegerField() 
num = models.DecimalField(max_digits=30,decimal_places=10) 
# 总长度为30，小数点后面10位 

# 枚举 c
olor_list = ( 
    (1,'黑色'), 
    (2,'白色'), 
    (3,'蓝色')
) 
color = models.IntegerField(choices=color_list) 
class Meta: 
    unique_together=( 
        ('email','username')  # 字段联合唯一索引 
) 
index_together=( 
    ('email','username')   # 联合索引，不做约束限制 
)

的∂
```
``` python

# admin模块 
from django.contrib 
import admin from app01 
import models admin.site.register(models.UserAdmin)

```
<h2 id="a12">
<a id="_2356"></a>分页查询</h2>


内置分页函数


urls
``` python
url(r'^index.html$', views.index),

```

views
``` python

from django.core.paginator 
import Paginator,Page,PageNotAnInteger,EmptyPage 

def index(request): 
    user_list = models.UserInfo.objects.all() 
#获得所有数据库数据 
paginator = Paginator(user_list,10) 

#设置每页显示的总条数 
current_page = request.GET.get('page')

# 获得当前页数 
try: 
    #设置当前页数对应的数据 
    posts = paginator.page(current_page) 
except PageNotAnInteger as e: 
    #当前页面数非整数 
    posts = paginator.page(1) 
except EmptyPage as e: 
    #当前页码数为空 
    posts = paginator.page(1) 
    
return render(request,'index.html',{'posts':posts})

```

<h2 id="a13">
<a id="ORM_2521"></a>ORM补充之基本操作（数据行高级操作）</h2>


排序

``` python

user_list = models.UserInfo.objects.all().order_by('-id','name') 
# —id代表降序，id代表升序

```


分组

``` python

from django.db.models 
import Count,Sum,Max,Min 
v =models.UserInfo.objects.values('ut_id').annotate(xxxx=Count('id')) 
# 等价于
    SELECT 
        `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
    FROM 
        `app01_userinfo` 
    GROUP BY 
        `app01_userinfo`.`ut_id` 
    ORDER BY 
        NULL

```
``` python

# 带有having 分组条件过滤 
v =models.UserInfo.objects.values('ut_id').annotate(xxxx=Count('id')).filter(xxxx__gt=2) 
# 等价于
    SELECT 
        `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
    FROM 
        `app01_userinfo` 
    GROUP BY 
        `app01_userinfo`.`ut_id` 
    HAVING 
        COUNT(`app01_userinfo`.`id`) > 2 
    ORDER BY 
        NULL

```
``` python

v =models.UserInfo.objects.filter(id__gt=2).values('ut_id').annotate(xxxx=Count('id')).filter(xxxx__gt=2) 
# 等价于
    SELECT 
        `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
    FROM 
        `app01_userinfo` 
    WHERE 
        `app01_userinfo`.`id` > 2 
    GROUP BY 
        `app01_userinfo`.`ut_id` 
    HAVING 
        COUNT(`app01_userinfo`.`id`) > 2 
    ORDER BY 
        NULL

```
``` python

分组格式：model.类名.objects.values(显示的字段名).annotate(作为字段查结结果的别名=Count(字段id/1)) 
# annotate依赖于values

```


条件过滤

``` python

models.UserInfo.objects.filter(id__gt=1) # id>1 
……（id__lt=1） # id<1 
……(id__lte=1) # id<=1 
……(id__gte=1) # id>=1 
……(id__in=[1,2,3]) # id in [1,2,3] 
……(name__startswith='xxxx') # 
……(name__contains='xxxx') # 
……exclude(id=1) # 
not in (id=1)

```


F,Q,extra方法


F
``` python

from django.db.models import F 
models.UserInfo.objects.all().update(age=F("age")+1) 
# F()用来取对象中某列值

```

Q(构造复杂的查询条件)
``` python

# 对象方式(不推荐) 
from django.db.models import Q 
models.UserInfo.objects.filter(Q(id__gt=1)) 

# or 
models.UserInfo.objects.filter(Q(id=8) | Q(id=2)) 

# and
models.UserInfo.objects.filter(Q(id=8) & Q(id=2)) 

```

方法方式
``` python

from django.db.models import Q 
q1 = Q() 
q1.connector = 'OR' 
q1.children.append(('id__gt', 1)) 
q1.children.append(('id', 10)) 
# 通过OR将3个条件进行连接组装 
q2 = Q() 
q2.connector = 'OR' 
q2.children.append(('c1', 1)) 
q2.children.append(('c1', 10)) 
q3 = Q() q3.connector = 'AND' 
#通过AND将2个条件进行连接组装 
q3.children.append(('id', 1)) 
q3.children.append(('id', 2)) 
q1.add(q3,'OR') 
#还可将q3嵌入到q1条件组中 
# 将q1和q2条件组通过AND汇总到一起，q1和q2内部分别用or组合条件 
con = Q() 
con.add(q1, 'AND') 
con.add(q2, 'AND')

```

方法方式实际应用（多条件组合查询时）
``` python

condition_dict = { #用户将选择的条件组合成字典格式 
    'k1':[1,2,3,4], 
    'k2':[1,], 
} 
con = Q() 
for k,v in condition_dict.items(): 
    q = Q() 
    q.connector = 'OR' 

for i in v: 
    q.children.append(('id', i)) 
    con.add(q,'AND') 

models.UserInfo.objects.filter(con)
*********************************************************************** 
q1 = Q() 
q1.connector = 'OR' 
q1.children.append(('id', 1)) 
q1.children.append(('id', 10)) 
q1.children.append(('id', 9)) 
q2 = Q() 
q2.connector = 'OR' 
q2.children.append(('c1', 1)) 
q2.children.append(('c1', 10)) 
q2.children.append(('c1', 9)) 
q3 = Q() 
q3.connector = 'AND' 
q3.children.append(('id', 1)) 
q3.children.append(('id', 2)) 
q1.add(q3,'OR') 
con = Q() 
con.add(q1, 'AND') 
con.add(q2, 'AND') 
#以上构造结果等介于(id=1 or id = 10 or id=9 or (id=1 and id=2)) and (c1=1 or c1=10 or c1=9)

```

extra(添加额外的自定义sql语句)
``` python

models.UserInfo.objects.extra(self, select=None, where=None, params=None, tables=None, order_by=None, select_params=None) 
a. 映射 
    select 
    select_params=None 
    select 此处 from 表 

b. 条件 
    where=None 
    params=None, 
    select * from 表 where 此处 

c. 表 
tables select * from 表,此处 

c. 排序 
order_by=None 
select * from 表 order by 此处

```
``` python

v = models.UserInfo.objects.all().extra( 
    select={ 
        'n':"select count(1) from app01_usertype where id=%s or id=%s", 
        'm':"select count(1) from app01_usertype where id=%s or id=%s", 
    }, 
    select_params=[1,2,3,4]
) 
for obj in v: 
    print(obj.name,obj.id,obj.n) 
    ---------- 等价于将查询结果作为字段显示列： 
# select 
# id, 
# name, 
# (select count(1) from tb) as n 
# from xb where ....

```
``` python

models.UserInfo.objects.extra( 
    select={
        'newid':'select count(1) from app01_usertype where id>%s'
    }, 
    select_params=[1,], 
    where = ['age>%s'], 
    params=[18,], 
    order_by=['-age'], 
    tables=['app01_usertype']
) 
---------- 等价于原生sql语句如下： 
select 
    app01_userinfo.id, 
    (select count(1) from app01_usertype where id>1) as newid 
from 
    app01_userinfo,app01_usertype 
where 
    app01_userinfo.age > 18 
order by 
    app01_userinfo.age 
desc

```
``` python

result = models.UserInfo.objects.filter(id__gt=1).extra( 
        where=['app01_userinfo.id < %s'], 
        params=[100,], 
        tables=['app01_usertype'], 
        order_by=['-app01_userinfo.id'], 
        select={
            'uid':1,
            'sw':"select count(1) from app01_userinfo"
        } #添加查询字段 
    ) 
    ---------- 
    SELECT (1) AS "uid", 
        (select count(1) from app01_userinfo) AS "sw", 
        "app01_userinfo"."id", 
        "app01_userinfo"."name", 
        "app01_userinfo"."age", 
        "app01_userinfo"."ut_id" 
    FROM 
        "app01_userinfo" , 
        "app01_usertype" 
    WHERE 
        ("app01_userinfo"."id" > 1 AND (app01_userinfo.id < 100)) 
    ORDER BY 
        ("app01_userinfo".id) 
    DESC

```

取特定字段值
``` python

v = models.UserInfo.objects.all().only('id','name') 
# 获取字段以外的字段会再发出第二次sql请求

```

取当前字段以外的所有值
``` python

v = models.UserInfo.objects.all().defer('name')

```

反转
``` python

v = models.UserInfo.objects.all().order_by('-id','name').reverse() 
# 只有在order_by()方法时才有效果

```

使用数据库引擎
``` python
models.UserInfo.objects.all().using('db2')

```

聚合
``` python

#统计总数 
from django.db.models import Count 
result = models.UserInfo.objects.aggregate(k=Count('ut_id', distinct=True), n=Count('id')) 
# distinct代表去重 print(ruselt.query())

```

以字典格式添加数据
``` python
obj = models.UserType.objects.create(**{'title': 'xxx'})

```

以关键字参数添加数据
``` python
obj = models.UserType.objects.create(title='xxx')

```

批量增加数据
``` python

objs = [ models.UserInfo(name='r11'), ] 
models.UserInfo.objects.bulk_create(objs, 10) 
# 10为一次提交10次数据，建议不超过999

```

创建/获取
``` python

obj, created = models.UserInfo.objects.get_or_create( 
    #如果存在数据则获取，否则直接创建 
    username='root1', 
    pwd='ff', 
    defaults={'email': '1111111','u_id': 2, 't_id': 2}
)

```

条件范围
``` python

models.UserInfo.objects.in_bulk([1,2,3]) 
#根据主键进行查询 相当于
models.UserInfo.objects.filter(id__in=[1,2,3])

```

raw(书写原生sql语句)
``` python

name_map = {'title': 'name'} 
# 将下面的title转换为name 
v1 = models.UserInfo.objects.raw('SELECT id,title FROM app01_usertype',translations=name_map) 
for i in v1: 
    print(i,type(i))

```

select_related:查询主动做连表，一次性获取所有连表中的数据（性能相关：数据量少的情况下使用）
``` python

q = models.UserInfo.objects.all().select_related('ut','gp') 
#等价于select * from userinfo inner join usertype on ... for row in q: print(row.name,row.ut.title) 
#采用.的形式获取连表数据

```

prefetch_related：不做连表，但会做多次查询（性能相关：数据量多，查询频繁下使用）
``` python

q = models.UserInfo.objects.all().prefetch_related('ut') 
# select * from userinfo; 
# Django内部：ut_id = [2,4] 
# select * from usertype where id in [2,4] for row in q: print(row.id,row.ut.title)

```
<h2 id="a14">
<a id="XSS_2863"></a>XSS攻击（跨站脚本攻击）</h2>
``` python

模拟攻击时：前提需要将对应设置注释 MIDDLEWARE = [ 
# 'django.middleware.csrf.CsrfViewMiddleware', 
]

```


urls

``` python
url(r'^index/', views.index), url(r'^comment/', views.comment),

```


views

``` python

def comment(request): 
    if request.method == "GET": 
        return render(request,'comment.html') 
    else: 
        v = request.POST.get('content') msg.append(v) 
    
    return render(request,'comment.html') 
    
def index(request): 
    return render(request,'index.html',{'msg':msg})

```
``` python

# 同时也可在视图函数中添加safe： 
def test(request): 
    from django.utils.safestring import mark_safe 
    temp = "<a href='http://www.baidu.com'>百度</a>" 
    newtemp = mark_safe(temp) 
    #将内容处理成safe安全数据 
    return render(request,'test.html',{'temp':newtemp})

```
``` python
黑客可通过伪造网站，进行xss攻击，获得用户访问正式网站中的cookies， 从而伪装该用户可到正式网站进行操作，所以cookies很重要，要xss要处于启动状态（默认xss为启用状态）

```

CSRF(跨站请求伪装攻击)


urls

``` python

# 前提需要setting文件中crsf验证开启 url(r'^csrf1.html$', views.csrf1)

```


views

``` python

def csrf1(request): 
    if request.method == 'GET': 
        return render(request,'csrf1.html') 
    else: 
        return HttpResponse('ok')

```


补充：csrf第二种处理方式：添加装饰器

``` python

from django.views import View 
from django.utils.decorators import method_decorator 
# CBV装饰器，无法将内置的@csrf_protect装饰器直接应用到函数上，而需手动写装饰器进行应用 
def wrapper(func): 
    def inner(*args,**kwargs): 
        return func(*args,**kwargs) 
    return inner

```
``` python

#在指定方法上添加装饰器 
class Foo(View):
    @method_decorator(wrapper) 
    def get(self,request): 
        pass 
        
    def post(self,request): 
        pass

```
``` python

# 在类上添加 
@method_decorator(wrapper,name='XXX') 
#name表示应用的函数名称 
class Foo(View): 
    def get(self,request): 
        pass 
    def post(self,request): 
        pass

```


views

``` python

def csrf1(request): 
if request.method == 'GET': 
    return render(request,'csrf1.html') 
else: 
    return HttpResponse('ok')

```
<h2 id="a15">
<a id="ORMhtmlsimple_tag_3031"></a>ORM函数相关（html模版上使用函数）simple_tag</h2>

setting注册程序块
``` python
INSTALLED_APPS = [ …… 'app01', ]

```

总结:
``` python
- simple_filter - 最多两个参数,格式： {{第一个参数|函数名称:"第二个参数"}} - 可以做条件判断 - simple_tag - 无限制: {% 函数名 参数 参数%}

```
<h2 id="a16">
<a id="cookiesessionsession_3137"></a>cookie和session（推荐使用session）</h2>
``` python
cookie是保存在客户端浏览器上的键值对， Session是保存在服务端的数据（本质是键值对） 因为单独使用cookies，它会保留用户具体的明文形式（不转化成字符串的敏感信息）发送给浏览器（不安全），所以推荐使用session， session发送的是随机字符串，不包含用户敏感信息（安全），其中session依赖于cookies,

```


urls

``` python

urlpatterns = [ 
    …… 
    url(r'^login/', views.login), 
    url(r'^index/', views.index),
]

```


views

``` python

def login(request): 
    if request.method == 'GET': 
        return render(request,'login.html') 
    else: 
        u = request.POST.get('user') 
        p = request.POST.get('pwd') 
        obj = models.UserAdmin.objects.filter(username=u,password=p).first() 
        if obj: 
            # 1. 生成随机字符串 
            # 2. 通过cookie发送给客户端 
            # 3. 服务端保存 
            # { # 随机字符串1: 
                {
                    'username':'alex',
                    'email':'x'
                    ...
                } 
            # } request.session['username'] = obj.username 
            return redirect('/index/') 
        else: 
            return render(request,'login.html',{'msg':'用户名或密码错误'}) 

def index(request): 
    # 获取客户端cookies中的随机字符串，去session中查找有无该字符串， 再通过字符串去session对应key的value中查看是有username，并获得它对应的值 
    v = request.session.get('username') 
    # v为username对应的具体值 
    if v:   
        return HttpResponse('登录成功:%s' %v) 
    else: 
        return redirect('/login/')

```


setting

``` python

# SESSION_COOKIE_NAME = "sessionid" 
# Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串 
# SESSION_COOKIE_PATH = "/" 
# Session的cookie保存的路径 
# SESSION_COOKIE_DOMAIN = None 
# Session的cookie保存的域名 
# SESSION_COOKIE_SECURE = False 
# 是否Https传输cookie 
# SESSION_COOKIE_HTTPONLY = True 
# 是否Session的cookie只支持http传输 
# SESSION_COOKIE_AGE = 1209600 
# Session的cookie失效日期（2周） 
# SESSION_EXPIRE_AT_BROWSER_CLOSE = False 
# 是否关闭浏览器使得Session过期 
# SESSION_SAVE_EVERY_REQUEST = False（推荐True） 
# 是否每次请求都保存Session，默认修改之后才保存 SESSION_ENGINE = 'django.contrib.sessions.backends.cashe' 
#引擎，缓存+数据库，推荐使用 SESSION__CASHE_ALLAS ='default' 
# 使用缓存别名

```
<h2 id="a17">
<a id="demo_3207"></a>用户登陆demo</h2>


models

``` python

from django.db 
import models 

class Boy(models.Model): 
    nickname = models.CharField(max_length=32) 
    username = models.CharField(max_length=32) 
    password = models.CharField(max_length=63) 
    
class Girl(models.Model): 
    nickname = models.CharField(max_length=32) 
    username = models.CharField(max_length=32) 
    password = models.CharField(max_length=63) 
    
class B2G(models.Model): 
    b = models.ForeignKey(to='Boy', to_field='id',on_delete='') 
    g = models.ForeignKey(to='Girl', to_field='id',on_delete='')

```


urls

``` python

urlpatterns = [ 
    url('admin/', admin.site.urls), 
    url(r'^login.html$', account.login), 
    url(r'^index.html$', love.index), 
    url(r'^loginout.html$',account.loginout), 
    url(r'^others.html$',love.others), 
]

```


views(创立文件夹的形式用来区分模块关系)


account模块
``` python

from django.shortcuts import render,HttpResponse,redirect 
from app01 import models 

def login(request): 
    #  用户登陆 
        :param request: 
        :return: 
        
    #  
    if request.method == 'GET': 
        return render(request,'login.html') 
    else: 
        user = request.POST.get('username') 
        pwd = request.POST.get('password') 
        gender = request.POST.get('gender') 
        rmb = request.POST.get('rmb') 
    # 性别判断 
    if gender == "1":   
        obj = models.Boy.objects.filter(username=user,password=pwd).first() 
    else: 
        obj = models.Girl.objects.filter(username=user,password=pwd).first() 
        if not obj: 
            # 未登录 
            return render(request,'login.html',{'msg': '用户名或密码错误'}) 
        else: 
            request.session['user_info'] = {'user_id':obj.id,'gender':gender,'username':user,'nickname':obj.nickname} 
            
        return redirect('/index.html') 
    
def loginout(request): 
    #  注销 :
        param request: 
        :return: 
    #  
    if request.session.get('user_info'): 
        request.session.clear() 

    # 清除服务端数据库session（推荐） 
    # request.session.delete(request.session.session_key) 
    #清除客户端session return redirect('/login.html')

```

love模块
``` python

from django.shortcuts import render,redirect,HttpResponse 
from app01 import models 

def index(request): 
    #  
    首页信息展示 
    :param request: 
    :return: 
    #  
    
    if not request.session.get('user_info'): 
        # 获取浏览器中的session随机字符串 
        return redirect('/login.html') else: 
        # 男：女生列表 
        # 女：男生列表 
        gender = request.session.get('user_info').get('gender') 
        if gender == '1': 
            user_list = models.Girl.objects.all() 
        else: 
            user_list = models.Boy.objects.all() 
        
        return render(request,'index.html',{'user_list':user_list}) 
            
def others(request): 
    #  
    获取与当前用户有关系的异性 
    :param request: 
    :return: 
    #  
    current_user_id = request.session.get('user_info').get('user_id') 
    gender = request.session.get('user_info').get('gender') 
    if gender == '1': 
        user_list = models.B2G.objects.filter(b_id=current_user_id).values('g__nickname') 
    else: 
        user_list = models.B2G.objects.filter(g_id=current_user_id).values('b__nickname') 
        print('result', user_list) 

    return render(request,'others.html',{'user_list':user_list})

```

建立html组件：user_header

urls
``` python

urlpatterns = [ 
    …… 
    url(r'^login/$', views.login), 
]

```


views

``` python

from django.forms import Form,fields 
# -------定义Form验证规则类 
class LoginForm(Form): 
    # 正则验证: 不能为空,6-18 
    username = fields.CharField( 
        max_length=18, 
        min_length=6, 
        required=True, 
        error_messages={ 
            'required': '用户名不能为空', 
            'min_length': '太短了', 
            'max_length': '太长了', 
        } 
    ) 
    # 正则验证: 不能为空，16+ 
    password = fields.CharField(min_length=16,required=True) 

    def login(request): 
        #Form表单提交形式 
        if request.method == "GET": 
            return render(request,'login.html') 
        else: 
            obj = LoginForm(request.POST) 
        #将提交的数据交给Form组件验证 
        if obj.is_valid(): 
            # 用户输入格式正确 
            print(obj.cleaned_data) 
            # 字典类型,只包含Form组件校验后的字段数据 
            return redirect('http://www.baidu.com') 
        else: 
            # 用户输入格式错误 
            return render(request,'login.html',{'obj':obj})

```


Form验证流程分析

``` python

第一步：
实例化，将字段转换为self.fields格式 LoginForm实例化时， 
self.fields={ 'user': 正则表达式 'pwd': 正则表达式 }

```
``` python

第二步：
循环self.fields，获得字段 
flag = True errors cleaned_data for k,v in self.fields.items(): 
input_value = request.POST.get(k) 
# 循环获得k字段的值（k需与前端字段名保持一致） 
校验input_value的值是否匹配正则表达式 
flag = False return flag 
--------------------------------------- 
if obj.is_valid(): 
    #返回结果为True则通过验证 
    print(obj.cleaned_data) 
else: 
    print(obj.errors) 
    
return render(request,'login.html')

```

urls
``` python

…… 
url(r'^ajax_login/', views.ajax_login),

```

views
``` python

class LoginForm(Form): 
    #定义Form组件类，用来验证请求数据 
    user = fields.CharField(required=True,min_length=6) 
    pwd = fields.CharField(min_length=18) 
    
    def ajax_login(request): 
        import json 
        ret={'status': True,'msg':None} 
        obj = LoginForm(request.POST) 
        if obj.is_valid(): 
            print(obj.cleaned_data) 
        else: 
            ret['status']=False 
            ret['msg']=obj.errors 
        # 获得字典，k为字段名，v为错误信息列表 
        v=json.dumps(ret) 
        print(obj.errors) 
        #print时，会自动调用__str__(),在该方法中将字典组装成了ul标签 
        return HttpResponse(v)

```


Form组件常用字段及参数

``` python

class TestForm(Form): 
    t1 = fields.CharField( 
        required=True, 
        max_length=8, 
        min_length=2, 
        error_messages={ 
            'required': '不能为空', 
            'max_length': '太长', 
            'min_length': '太短', 
        } ) 
    t2 = fields.IntegerField( 
        min_value=10, 
        max_value=1000, 
        error_messages={ 
            'required': 't2不能为空', 
            'invalid': 't2格式错误，必须是数字', 
            'min_value': '必须大于10', 
            'max_value': '必须小于1000', 
        }, ) 
    t3 = fields.EmailField( 
        error_messages={ 
            'required': 't3不能为空', 
            'invalid': 't3格式错误，必须是邮箱格式', 
        } ) 
# 为空，长度，格式，正则
    t4=fields.URLField() 
    t5=fields.SlugField() 
    t6=fields.GenericIPAddressField() 
    t7=fields.DateField() 
    t8=fields.DateTimeField() 
    t9=fields.RegexField('139\d+') 
# 自定义正则表达式的字段验证规则

```
``` python

生成HTML标签： 
widget=widgets.Select, 
******** 用于指定生成怎样的HTML，select，text,input/. label='用户名', 
# obj.t1.label disabled=False, 
# 是否可以编辑 label_suffix='--->', 
# Label内容后缀 需要在html模版中添加{{ obj.as_p }}来显示出所有Form类中定义的字段 initial='666', 
# 无用，猜测有问题应该在input框中显示默认值 help_text='。。。。。。', 
# 提供帮助信息

```

urls
``` python
url(r'^register/', views.register),

```

views
``` python

class RegiterForm(Form): 
    user = fields.CharField(min_length=8) 
    email = fields.EmailField() 
    password = fields.CharField() 
    phone = fields.RegexField('139\d+') 
    
    def register(request): 
        if request.method == 'GET': 
            obj = RegiterForm() 
            return render(request,'register.html',{'obj':obj}) 
        # 只返回表单组件类的HTML文本，无数值返回 
        else: 
            obj = RegiterForm(request.POST) 
        # 因用户提交了数据，Form组件会返回带有输入框值的HTML文本 
        if obj.is_valid(): 
            print(obj.cleaned_data) 
        else: 
            print(obj.errors) 
            return render(request,'register.html',{'obj':obj})

```
<h2 id="a19">
<a id="Form_3648"></a>Form组件完成学员管理系统</h2>


models

``` python

from django.db import models 

class Classes(models.Model): 
#默认生成id 
    title = models.CharField(max_length=32) 
    
    def __str__(self): 
# 重载方法，打印对象时，打印的内容为对象包含的title属性 
        return self.title 
        
class Student(models.Model): 
    name = models.CharField(max_length=32) 
    email = models.CharField(max_length=32) 
    age = models.IntegerField(max_length=32) 
    cls = models.ForeignKey('Classes',on_delete='') 
    
class Teacher(models.Model): 
    tname = models.CharField(max_length=32) 
    c2t = models.ManyToManyField('Classes') 
# 自动生成第3张关联表，表中自动生成两张表的外键关联id

```


班级管理


查
``` python

# urls url(r'^class_list/',views.class_list),

```
``` python

# views 
def class_list(request): 
    cls_list = models.Classes.objects.all() 
    return render(request,'class_list.html',{'cls_list':cls_list})

```

增
``` python

# urls url(r'^add_class/',views.add_class),

```
``` python

# views 
class ClassForm(Form): 
    title = fields.RegexField('全栈\d+') 
# 定义From组件校验规则 
    def add_class(request): 
        if request.method == 'GET': 
            obj = ClassForm() 
            # 用Form组件生成表单标签到页面上 
            return render(request,'add_class.html',{'obj':obj}) 
            
        else: 
            obj = ClassForm(request.POST) 
            # 通过Form组件进行校验 
            if obj.is_valid(): 
                models.Classes.objects.create(**obj.cleaned_data) 
                return redirect('/class_list/') 
            return render(request, 'add_class.html', {'obj': obj})

```

改
``` python

# urls url(r'edit_class/(\d+)',views.edit_class),
#接收任意数字id的正则

```
``` python

# views 
…… 省略Form组件定义类 …… 
def edit_class(request, nid): 
    if request.method == 'GET': 
        row = models.Classes.objects.filter(id=nid).first() 
        # 让页面显示初始值 
        # obj = ClassForm(data={'title': row.title}) 
        #发送到前端时，Form组件会进行校验 
        obj = ClassForm(initial={'title': row.title}) 
        #Form组件不会进行校验
        return render(request,'edit_class.html',{'nid':nid,'obj':obj}) 
    else: 
        obj = ClassForm(request.POST) if obj.is_valid(): 
        # models.Classes.objects.filter(id=nid).update(title = obj.cleaned_data['title']) 
        models.Classes.objects.filter(id=nid).update(**obj.cleaned_data) 
        # 以字典格式插入数据 
        return redirect('/class_list/') 
    return render(request,'edit_class.html',{'nid': nid,'obj':obj})

```


学生管理


查
``` python

# urls url(r'^student_list/', views.student_list),

```
``` python

# views 
def student_list(request): 
    stu_list = models.Student.objects.all() 
    return render(request, 'student_list.html', {'stu_list':stu_list})

```

增
``` python

# urls url(r'add_student/', views.add_student),

```
``` python

# views # Form组件定义： 
class StudentForm(Form): 
    name = fields.CharField( 
        min_length=2, 
        max_length=6, 
        widget=widgets.TextInput(attrs={'class': 'form-control'}) #表单组件样式选择及属性设置，默认组件样式为text文本框 
    ) 
    email = fields.EmailField(
        widget=widgets.TextInput(attrs={'class': 'form-control'})
    ) 
    age = fields.IntegerField(
        min_value=18,
        max_value=25,
        widget=widgets.TextInput(attrs={'class': 'form-control'})
    ) 
    cls_id = fields.IntegerField( # widget=widgets.Select(choices=[(1,'上海'),(2,'北京')]) 
        widget=widgets.Select(
            choices=models.Classes.objects.values_list('id','title'),
            attrs={'class': 'form-control'}
        ) # 获得单选下拉框 
        # 另外一种写法：
    cls_id=fields.ChoiceField( 
        choices = models.Class.objests.values_list('id','title') 
        widget = widgets.Select(attr={'class':''form-control}) ) 
    ) 
    def add_student(request): 
        if request.method == 'GET': 
            obj = StudentForm() 
            return render(request,'add_student.html',{'obj':obj}) 
        else: 
            obj = StudentForm(request.POST) 
            if obj.is_valid(): 
                models.Student.objects.create(**obj.cleaned_data) 
                return redirect('/student_list/') 
            else: 
                return render(request,'add_student.html',{'obj':obj})

```

改
``` python
# urls url(r'^edit_student/(\d+)/', views.edit_student),

```
``` python

# views 
……省略Form组件…… 
def edit_student(request,nid): 
    if request.method == 'GET': 
        row = models.Student.objects.filter(id=nid).values('name','email','age','cls_id').first() 
        #如果不添加first会报错，错误为“要解压的值太多” 
        obj = StudentForm(initial=row) 
        # 将数据封装至Form组件中，initial设置为不做数据验证 
        return render(request,'edit_student.html',{'nid':nid, 'obj':obj}) 
    else: 
        obj = StudentForm(request.POST) 
        if obj.is_valid(): 
            models.Student.objects.filter(id=nid).update(**obj.cleaned_data) 
            return redirect('/student_list/') 
        else: 
            return render(request,'/edit_student.html',{'nid':id, 'obj':obj})

```


老师管理


查
``` python
# urls url(r'^teacher_list/', views.teacher_list),

```
``` python

# views 
def teacher_list(request): 
    tea_list = models.Teacher.objects.all() 
    return render(request,'teacher_list.html',{'tea_list':tea_list})

```

增
``` python
# urls url(r'^add_teacher/', views.add_teacher),

```
``` python

# views #Form组件定义：（实现数据动态显示） 
class TeacherForm(Form): 
    tname=fields.CharField(min_length=2) 
    cls_id = fields.MultipleChoiceField( 
        #　多选模式，过滤出字典中包含的是列表格式数据{'cls_id': ['2', '3']}，而非列表字符串格式{'cls_id': “['2', '3']”} 
        # choices=models.Classes.objects.values_list('id','title'), 
        # 生成下拉框对应的值，有了__init__()构造方法后可以不用写choices关键字参数 widget=widgets.SelectMultiple 
        # 多选下拉框表单组件 
    ) 
    #因From组件对象不会重新启动获得数据库的值， #所以需要每一次类加载实例化的构造方法来重新获取数据库的值,实现数据动态显示 
    def __init__(self,*args,**kwargs): 
        super(TeacherForm,self).__init__(*args,**kwargs) 
        # 调用父类构造方法 
        self.fields['cls_id'].widget.choices=models.Classes.objects.values_list('id','title') 
        # 获得字典中字段的插件widget中的choices 
        # Form组件执行方式 
        # obj = TeacherForm() 
        # 1. 找到所有字段 
        # 2. self.fields = { 
            # 加载字典中的所有字段 
            # tname: fields.CharField(min_length=2) 
            # } 
        def add_teacher(request): 
        if request == 'GET': 
            obj = TeacherForm() return render(request,'add_teacher.html',{'obj':obj}) 
        else: 
            obj = TeacherForm(request.POST) 
            if obj.is_valid(): 
                cls_id= obj.cleaned_data.pop('cls_id') 
                # 单独提取出cls_id的值 
                row = models.Teacher.objects.create(**obj.cleaned_data) 
                # **字典,会自动将字典格式{'tname': 'tom'}转换成tname='tom'格式的数据 
                row.c2t.add(*cls_id) 
                # *代表列表格式插入 
                return redirect('/teacher_list/') 
            return render(request,'add_teacher.html',{'obj':obj})

```

改
``` python
# urls url(r'^edit_teacher/(\d+)/', views.edit_teacher),

```
``` python

# views 
def edit_teacher(request,nid): 
    if request.method == "GET": 
        row = models.Teacher.objects.filter(id=nid).first() 
        class_ids = row.c2t.values_list('id') 
        #获得关联的班级id,值为[(3,),(1,)] 
        # zip()将[(3,),(1,)]转换为[（3，1),] 
        id_list = list(zip(*class_ids))[0] 
        if list(zip(*class_ids)) else [] 
        # obj = TeacherForm(initial={'tname':row.tname,'xx':[1,2,3]}) 
        obj = TeacherForm(initial={'tname':row.tname,'cls_id':id_list}) 
        return render(request,'edit_teacher.html',{'obj':obj})

```
<h2 id="a20">
<a id="Form_4101"></a>Form常用组件定制</h2>
``` python

class TestForm(Form): 
    t1 = fields.MultipleChoiceField(  # 验证多选框的值 
        choices =[(1,'篮球'),(2,'足球')],  # 制定显示值 
        widget=widgets.CheckboxSelectMultiple  # 生成多选框组件 
    ) 
    t2 =fields.MultipleChoiceField( 
        choices=[(1,'篮球'),(2,'足球')], 
        widget=widgets.RadioSelect  # 单选按钮组件 
    ) 
    t3 = fields.FileField( 
        widget=widgets.FileInput  # 文件输入框 
    )

```
<h2 id="a21">
<a id="Form_4121"></a>Form组件中的钩子（扩展自定义函数）</h2>
``` python

class TestForm(Form): 
    user = fields.CharField( # 添加自定义正则表达式 
        validators=[RegexValidator(r'^[0-9]+$', '请输入数字'), 
        RegexValidator(r'^159[0-9]+$', '数字必须以159开头')],
    ) 
    email = fields.EmailField() 
    
    def clean_user(self): 
        # 常用于扩展用户名是否在数据库中存在,验证码是否匹配，传参request 
        v = self.cleaned_data['user'] 
        if models.Student.object.filter(name=v).count(): 
            raise ValuedationError('用户已存在') 
            return self.cleaned_data['user']
            
    def clean(self): 
        # 常用于字段的联合唯一判断 
        user=self.cleaned_data.get('user') 
        email=self.cleaned_data.get('email') 
        if models.Stuent.objects.filter(user=user,email=email).count(): 
            raise ValuedationError('用户名和邮箱联合已经存在') 
            return self.cleaned_data

```
<h2 id="a22">
<a id="Ajax_4147"></a>Ajax提交数据部分</h2>

views
``` python

import os 

def upload(request): 
    if request.method == 'GET': 
        return render(request, 'upload.html') 
    else: 
        file_obj = request.FILES.get('fafafa') 
        # 获得浏览器发送的文件 
        file_path = os.path.join('static', file_obj.name) 
        # 组装文件路径 
        with open(file_path, 'wb') as f: 
        for chunk in file_obj.chunks(): 
        # 把文件块以字节的形式写入文件 
            f.write(chunk) 
            
    return HttpResponse(file_path)

```


自定义API用作返回数据


urls
``` python
url(r'^users/', views.users),

```

views
``` python

def users(request): 
    print('请求到来') 
    callback = request.GET.get('funcname') 
    user_list=[ 'leo', 'tom', 'jack' ] 
    temp = '%s(%s)'%(callback, user_list) 
    print(temp) 
    # return HttpResponse(json.dumps(user_list)) 
    return HttpResponse(temp) 
    # 返回一个字符串对象

```

setting配置‘
``` python
ALLOWED_HOSTS = ['www.s4.com'] 
# 允许访问的主机

```

本地PC配置
``` python
C:\Windows\System32\drivers\etc 往hosts文件中添加内容：127.0.0.1 www.s4.com

```
<h2 id="a230">
<a id="CORS_4456"></a>CORS解决跨域问题（跨来源资源共享）（响应头添加值）</h2>


第三方服务器

``` python
url(r'^new_users/', views.new_users),

```
``` python

def new_users(request): 
    user_list = [ 'lleo', 'tom', 'jack' ] 
    obj = HttpResponse(json.dumps(user_list)) 
    # 添加令牌，添加响应头信息 
    obj["Access-Control-Allow-Origin"]='http://www.s5.com:8000' 
    # 允许跨站的数据响应返回，浏览器不设阻拦 
    # obj["Access-Control-Allow-Origin"]=”*“ print('请求') 
    return obj

```


复杂请求


第三方服务器
``` python

# 复杂请求的处理 
def new_users(request): 
    if request.method == 'OPTIONS': 
        obj = HttpResponse() 
        obj["Access-Control-Allow-Origin"] ='*' 
        obj["Access-Control-Allow-Origin"] ='DELETE'
        return obj 
    
    obj = HttpResponse('adsf') 
    obj["Access-Control-Allow-Origin"] = '*' 
    return obj

```