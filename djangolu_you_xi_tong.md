# Django路由系统

 简而言之，django的路由系统作用就是使views里面处理数据的函数与请求的url建立映射关系。使请求到来之后，根据urls.py里的关系条目，去查找到与请求对应的处理方法，从而返回给客户端http页面数据。
 
 ![](django_router_01.jpg)
 
##一、最基础的url映射

```
from django.conf.urls import patterns, include, url

from django.contrib import admin
admin.autodiscover()
from apptest import views

urlpatterns = patterns('',
    url(r'^admin/', include(admin.site.urls)),
    url(r'^index/$', views.index),
)

```

1. 先从创建的app下的views.py面定义处理数据的函数

2. 在urls.py里导入views

3. 在urlpatterns里写入一条url与处理函数的l映射关系

4. url映射一般是一条正则表达式，“^” 字符串的开始，“$“ 字符串的结束

5. 当写成”^$“时，不输入任何url时不会在返回黄页，而是返回后面函数里对应的页面。一般这一条会写在url的最后。如：


```
url(r'^```, views.index),

```

##二、按照顺序放置的动态路由

```
 urlpatterns = [
     url(r'^user/(\d+)$', views.user),
     url(r'^user_list/(\d+)/(\d+)$', views.user_list),
 
 ]
 
 ```
   
* ^user/(\d+)$ 

相对应的url是： ”http://127.0.0.1/uer/8“ (\d+)是匹配任意的数字，在分页时灵活运用。

*　^user_list/(\d+)/(\d+)$

相对应的url是： ”http://127.0.0.1/uer/8/9“，匹配到的数字会以参数的形式按照顺序传递给views里面相对应的函数

```
def user_list(request,nid,nid2):
 
     return HttpResponse(nid+nid2)
```

##三、传参形式的动态路由

利用正则表达式的分组方法，将url以参数的形式传递到函数，可以不按顺序排列。
 
 ```

  urlpatterns = [
 url(r'^user_list/(?P<v1>\d+)/(?P<v2>\d+)$',views.user_list),
 ]

  ```
 
 ```

(?P<v1>\d+)

```

正则表达式的分组，相当于一个字典， key=v1, value=\d+。 {"v1":"\d+"}
然后将此参数传递到views里对应的函数，可以不按照顺序

```

 def user_list(request,v2,v1): 
     return HttpResponse(v1+v2)
```


```
参数v1 = (?P<v1>\d+)

参数v2 = (?P<v2>\d+)

```
---

```
#注意（？P<v1>\d+）添加参数的好处是，可以设置默认值：

 url(r'^user_list/(?P<v1>\d+) views.test,{'v1':222})

```




##四、根据不同的app来分发不同的url

如果一个项目下有很多的app，那么在urls.py里面就要写巨多的urls映射关系。这样看起来很不灵活，而且杂乱无章。

我们可以根据不同的app来分类不同的url请求。

首先，在urls.py里写入urls映射条目。注意要导入include方法

```
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [

    url(r'^app01/', include('app01.urls')),

]
```

这条关系的意思是将url为”app01/“的请求都交给app01下的urls去处理

其次，在app01下创建一个urls.py文件，用来处理请求的url，使之与views建立映射

```
from django.conf.urls import include, url
from app01 import views

urlpatterns = [

    url(r'index/$', views.index),

]
```

##五、通过反射机制，为django开发一套动态的路由系统

在urls.py里定义分类正则表达式

```
from django.conf.urls import patterns, include, url
from django.contrib import admin
from DynamicRouter.activator import process

urlpatterns = patterns('',
    # Examples:
    # url(r'^$', 'DynamicRouter.views.home', name='home'),
    # url(r'^blog/', include('blog.urls')),

    url(r'^admin/', include(admin.site.urls)),
    
    
    ('^(?P<app>(\w+))/(?P<function>(\w+))/(?P<page>(\d+))/(?P<id>(\d+))/$',process),
    ('^(?P<app>(\w+))/(?P<function>(\w+))/(?P<id>(\d+))/$',process),
    ('^(?P<app>(\w+))/(?P<function>(\w+))/$',process),
    ('^(?P<app>(\w+))/$',process,{'function':'index'}),
)
```

在同目录下创建activater.py

```
#!/usr/bin/env python
#coding:utf-8

from django.shortcuts import render_to_response,HttpResponse,redirect


def process(request,**kwargs):
    '''接收所有匹配url的请求，根据请求url中的参数，通过反射动态指定view中的方法'''
    
    app =  kwargs.get('app',None)
    function = kwargs.get('function',None)
    
    try:
        appObj = __import__("%s.views" %app)
        viewObj = getattr(appObj, 'views')
        funcObj = getattr(viewObj, function)
        
        #执行view.py中的函数，并获取其返回值
        result = funcObj(request,kwargs)
        
    except (ImportError,AttributeError),e:
        #导入失败时，自定义404错误
        return HttpResponse('404 Not Found')
    except Exception,e:
        #代码执行异常时，自动跳转到指定页面
        return redirect('/app01/index/')
    
    return result
```