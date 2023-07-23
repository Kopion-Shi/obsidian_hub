# 学员管理系统

这一节让我们来快速的过一下Django的各个模块，在上一节内容中，你可能了解了Django所提供的功能，这一节我们来切实体会一下。你最好打开你熟悉的IDE，一起写起来。

## 需求

一句话就能描述清楚的需求：
提供一个学员管理系统，一个前台页面，展示现有学员，并供新学员提交申请，一个后台，能够处理申请。over

## 初始化环境

首先，创建虚拟环境: ``  conda create -n web_study_mansger python=3.8 ``

然后激活虚拟环境:    ```conda activate web_study_mansger```

## 创建项目

`cd` 到你喜欢的目录中，比如`//home/sxl/python_dev`，创建项目根目录: `mkdir web_study_mansger`，这是我们的项目目录，然后再我们创建项目结构: `cd sweb_study_mansger && django-admin startproject student_sys`，我们能得到下面的结构:
![[Pasted image 20230716130009.png]]


## 创建APP

进入`web_study_mansger`/student_sys`中，通过上一步创建好的manage.py创建一个app: `python manage.py startapp student`。现在目录结构如下:
![[Pasted image 20230716131207.png]]

## 编写代码

我们可以在Model层开始写代码了，这是一个简单的需求，我们只需要一个Model就可以满足。

`web_study_mansger/student_sys/student/models.py:

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import models


class Student(models.Model):
    SEX_ITEMS = [
        (1, '男'),
        (2, '女'),
        (0, '未知'),
    ]
    STATUS_ITEMS = [
        (0, '申请'),
        (1, '通过'),
        (2, '拒绝'),
    ]
    name = models.CharField(max_length=128, verbose_name="姓名")
    sex = models.IntegerField(choices=SEX_ITEMS, verbose_name="性别")
    profession = models.CharField(max_length=128, verbose_name="职业")
    email = models.EmailField(verbose_name="Email")
    qq = models.CharField(max_length=128, verbose_name="QQ")
    phone = models.CharField(max_length=128, verbose_name="电话")

    status = models.IntegerField(choices=STATUS_ITEMS, verbose_name="审核状态")

    created_time = models.DateTimeField(auto_now_add=True, editable=False, verbose_name="创建时间")

    def __unicode__(self):
        return '<Student: {}>'.format(self.name)

    class Meta:
        verbose_name = verbose_name_plural = "学员信息"
```

再来写admin.py:

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.contrib import admin

from .models import Student


class StudentAdmin(admin.ModelAdmin):
    list_display = ('id', 'name', 'sex', 'profession', 'email', 'qq', 'phone', 'status', 'created_time')
    list_filter = ('sex', 'status', 'created_time')
    search_fields = ('name', 'profession')
    fieldsets = (
        (None, {
         'fields': (
                 'name',
                 ('sex', 'profession'),
                 ('email', 'qq', 'phone'),
                 'status',
                 )
         }),
    )


admin.site.register(Student, StudentAdmin)
```

写完这两个配置，model和admin的界面就ok了，接下来我们把这个`student`app放到settings.py中。

我们只需要在INSTALLED_APPS配置的最后，或者最前面增加'student'即可:

settings.py文件:

```
INSTALLED_APPS = [
    'student',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

好了，后台部分就完成了，我们来创建下表以及超级用户，通过下面命令:

- `cd web_study_mansger/student_sys/`
- `python manage.py makemigrations` 创建迁移文件
- `python manage.py migrate` 创建表
- `python manage.py createsuperuser` 根据提示，输出用户名，邮箱，密码

启动项目: `python manage.py runserver`，访问: [http://127.0.0.1:8000，看到一个提示页，这是因为我们还没开发首页。我们可以进入到admin的页面](http://127.0.0.1:8000%EF%BC%8C%E7%9C%8B%E5%88%B0%E4%B8%80%E4%B8%AA%E6%8F%90%E7%A4%BA%E9%A1%B5%EF%BC%8C%E8%BF%99%E6%98%AF%E5%9B%A0%E4%B8%BA%E6%88%91%E4%BB%AC%E8%BF%98%E6%B2%A1%E5%BC%80%E5%8F%91%E9%A6%96%E9%A1%B5%E3%80%82%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E8%BF%9B%E5%85%A5%E5%88%B0admin%E7%9A%84%E9%A1%B5%E9%9D%A2/): [http://127.0.0.1:8000/admin/。用你创建好的账户登录，就能看到一个完整的带有CURD的后台了。](http://127.0.0.1:8000/admin/%E3%80%82%E7%94%A8%E4%BD%A0%E5%88%9B%E5%BB%BA%E5%A5%BD%E7%9A%84%E8%B4%A6%E6%88%B7%E7%99%BB%E5%BD%95%EF%BC%8C%E5%B0%B1%E8%83%BD%E7%9C%8B%E5%88%B0%E4%B8%80%E4%B8%AA%E5%AE%8C%E6%95%B4%E7%9A%84%E5%B8%A6%E6%9C%89CURD%E7%9A%84%E5%90%8E%E5%8F%B0%E4%BA%86%E3%80%82)

## 基础配置（中文）

通过上面的配置，你看到的界面应该是英文的，并且时区也是UTC时区。所以我们需要进一步配置。

在settings中有如下配置:

```
LANGUAGE_CODE = 'zh-hans'  # 语言

TIME_ZONE = 'Asia/Shanghai'  # 时区

USE_I18N = True  # 语言

USE_L10N = True  # 数据和时间格式

USE_TZ = True  # 启用时区
```

修改完这些之后，刷新下试试。你可以尝试修改上面的配置，看看分别对应什么功能。

到这一部分我们基本上完成了admin的部分。下一节我们来完成页面提交数据的部分，看下如何使用Form。

# 学员管理系统- 前台

## 开发首页

有了上一节的Model和Admin的部分，我们接着来做一个简单的用户提交申请的表单页面。

首先在student/views.py文件中编写下面的代码:

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.shortcuts import render


def index(request):
    words = 'World!'
    return render(request, 'index.html', context={'words': words})
```

上面的代码中，我们用了django提供的一个快捷的方法`render()`来渲染页面，使用模板`index.html`文件。我们需要在student目录下创建`templates`文件夹，这个文件夹是Django在渲染页面时会默认查找的。

这部分需要多说几句的是，Django查找会去每个App下，也就是我们的settings.py文件中配置的`INSTALLED_APPS`中的app下的`templates`文件夹中查找你在`render`上用到的模板，并且是顺序查找。这意味着，如果你有两个app，比如studentA, studentB，而这两个app中都存在`templates/index.html`，那么Django会加载位置在前的那个App的index.html文件。这个你可以自行尝试下。通过这种方式我们也可以重写admin的模板。

创建好`templates/index.html`之后，我们编写页面代码。简单来做:

```
<!DOCTYPE html>
<html>
    <head>
        <title>学员管理系统-by the5fire</title>
    </head>
    <body>
        Hello {{ words }}!
    </body>
</html>
```

接着我们需要再配置下url，也就是提供一个url映射，可以让用户访问url时把数据发送到我们定义的`index` 这个view上。

我们直接来修改student_sys目录下的urls.py文件:

```
from django.contrib import admin

from django.urls import path,re_path

from student.views import index

  

urlpatterns = [

    re_path(r'^$', index, name='index'),

    path(r'^admin/', admin.site.urls),

]
```

这样改完，我们再次启动项目: `python manage.py runserver`，访问:[http://127.0.0.1:8000就能看到我们输出的``Hello]
![[Pasted image 20230716143456.png]]
## 输出数据

接下来的工作就是把数据从表里面取出来，渲染到页面上了。你可以现在admin后台创建几条学员数据，以便于我们测试。

我们需要修改views.py中的代码:

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.shortcuts import render

from .models import Student


def index(request):
    students = Student.objects.all()
    return render(request, 'index.html', context={'students': students})
```

接着修改index.html中的代码:

```
<!DOCTYPE html>
<html>
    <head>
        <title>学员管理系统-by the5fire</title>
    </head>
    <body>
        <ul>
        {% for student in students %}
            <li>{{ student.name }} - {{ student.get_status_display }}</li>
        {% endfor %}
        </ul>
    </body>
</html>
```

这样我们就输出了一个简单的列表，展示学员名称和目前状态。这里有一个地方需要注意的是`{{ student.get_status_display }}`，在Model中我们只定义了`status`字段，并未定义这样的字段，为什么能通过这种方式取到数据呢。并且我们在Admin中，也没有使用这样的字段。

原因就是，对于设置了choices的字段，Django会帮我们提供一个方法（注意，是方法），用来获取这个字段对应的要展示的值。回头看下我们`status`的定义:

```
## 省略上下文代码

STATUS_ITEMS = [
    (0, '申请'),
    (1, '通过'),
    (2, '拒绝'),
]

status = models.IntegerField(choices=STATUS_ITEMS, verbose_name="审核状态")

## 省略上下文代码
```

在admin中，展示带有choices属性的字段时，Django会自动帮我们调用`get_status_display`方法，所以我们不用配置。而在我们自己写的模板中，我们需要自己来写。并且在模板中不支持函数/方法调用，你只需要写方法名称即可，后面的括号不需要写。Django会自行帮你调用(如果是方法的话)。

## 提交数据

输出数据之后，我们再来开发提交数据的功能。这部分我们用一下Form。

首先我们先创建一个forms.py的文件，跟views.py同级。编写如下代码:

```
# coding:utf-8
from __future__ import unicode_literals

from django import forms

from .models import Student


class StudentForm(forms.Form):
    name = forms.CharField(label='姓名', max_length=128)
    sex = forms.ChoiceField(label='性别', choices=Student.SEX_ITEMS)
    profession = forms.CharField(label='职业', max_length=128)
    email = forms.EmailField(label='邮箱', max_length=128)
    qq = forms.CharField(label='QQ', max_length=128)
    phone = forms.CharField(label='手机', max_length=128)
```

看这个`StudentForm`的定义是不是很熟悉，跟Model的定义类似，那么我们能不能复用Model的代码呢。答案是可以。还记得我们上节文档介绍的部分吗？有一个ModelForm可以用。我们来改下。

```
# coding:utf-8
from __future__ import unicode_literals

from django import forms

from .models import Student


class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = (
            'name', 'sex', 'profession',
            'email', 'qq', 'phone'
        )
```

只需要这么改就ok，不需要重复定义N多个字段。如果有修改对应字段类型的需求，比如把qq改成`IntegerField`用来做数字校验，也是可以声明出来。也可以通过定义`clean`方法的方式来做，我们来改下代码，增加QQ号必须为纯数字的校验:

```
# coding:utf-8
from __future__ import unicode_literals

from django import forms

from .models import Student


class StudentForm(forms.ModelForm):
    def clean_qq(self):
        cleaned_data = self.cleaned_data['qq']
        if not cleaned_data.isdigit():
            raise forms.ValidationError('必须是数字！')

        return int(cleaned_data)

    class Meta:
        model = Student
        fields = (
            'name', 'sex', 'profession',
            'email', 'qq', 'phone'
        )
```

其中`clean_qq`就是Django的form会自动调用，来处理每个字段的方法，比如在这个form中你可以通过定义`clean_phone`来处理电话号码，可以定义`clean_email`来处理邮箱等等。如果验证失败，可以通过`raise forms.ValidationError('必须是数字！')`的方式返回错误信息，这个信息会存储在form中，最终会被我们渲染到页面上。

有了form，我们接下来需要做的就是在页面中展示form，让用户能够填写信息提交表单。同时对于提交的数据，我们需要先做校验，通过后可以保存到数据库中。来看下views.py中的文件最终的样子：

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.http import HttpResponseRedirect
from django.urls import reverse
from django.shortcuts import render

from .models import Student
from .forms import StudentForm


def index(request):
    students = Student.objects.all()
    if request.method == 'POST':
        form = StudentForm(request.POST)
        if form.is_valid():
            cleaned_data = form.cleaned_data
            student = Student()
            student.name = cleaned_data['name']
            student.sex = cleaned_data['sex']
            student.email = cleaned_data['email']
            student.profession = cleaned_data['profession']
            student.qq = cleaned_data['qq']
            student.phone = cleaned_data['phone']
            student.save()
            return HttpResponseRedirect(reverse('index'))
    else:
        form = StudentForm()

    context = {
        'students': students,
        'form': form,
    }
    return render(request, 'index.html', context=context)
```

里面有一个`form.cleaned_data`，这个对象是Django的form对用户提交的数据根据字段类型做完转换之后的结果。另外还有`reverse`的使用，我们在urls.py中定义`index`的时候，声明了`name='index'`，所以我们这里可以通过`reverse`来拿到对应的url。这么做的好处是，不需要硬编码url到代码中，这意味着如果以后有修改url的需求，只要index的名称不变，这个地方的代码就不用改。

写完views.py中的代码之后，我们要把form传到模板中，这样用户才能最终看到一个可以填写数据的表单。要在模板中加form，是相当简单的一件事。最终模板(index.html)代码如下:

```
<!DOCTYPE html>
<html>
    <head>
        <title>学员管理系统-by the5fire</title>
    </head>
    <body>
        <h3><a href="/admin/">Admin</a></h3>
        <ul>
            {% for student in students %}
            <li>{{ student.name }} - {{ student.get_status_display }}</li>
            {% endfor %}
        </ul>
        <hr/>
        <form action="/" method="post">
            {% csrf_token %}
            {{ form }}
            <input type="submit" value="Submit" />
        </form>
    </body>
</html>
```

其中 `{% csrf_token %}`是Django对提交数据安全性做的校验，这意味着，如果没有这个token，提交过去的数据是无效的。这是用来防止跨站伪造请求攻击的一个手段。

只需要这么写，Django就会帮我们自动把所有字段列出来。当然，如果需要调整样式，那就要自己来增加css样式文件解决了。

不过到此为止，功能上已经完备。你可以再次通过命令: `python manage.py runserver`，

# 进阶部分

虽然是一个简单的Demo，但是有句老话叫：麻雀虽小五脏俱全，我们也得把常用的功能使用到。所以增加这一部分，包括：Class Base View, Middleware, TestCase这三个部分。

_注意_，如果你前面的例子没有跑起来，可以先不看这一节，先把前面的代码跑起来再说。不然，你可以能越学越乱。

## Class Based View

在如何阅读文档的部分，我又讲到，如果你有很多类似的view方法，那么你可以考虑抽象出一个ClassBased View来。这样可以更好的复用你的代码。

不过对于我们的需求来说，用ClassBased View不是很必要，我们只是演示用法。用类的方式有一个好处就是我们可以分离`get`和`post`的处理逻辑。回头看下上节`views.py`中的代码，其中有一个关于`request.method`的判断。我们来通过类级的View去掉层控制语句。

来看完整的views.py代码:

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.http import HttpResponseRedirect
from django.urls import reverse
from django.shortcuts import render
from django.views import View

from .models import Student
from .forms import StudentForm


class IndexView(View):
    template_name = 'index.html'

    def get_context(self):
        students = Student.objects.all()
        context = {
            'students': students,
        }
        return context

    def get(self, request):
        context = self.get_context()
        form = StudentForm()
        context.update({
            'form': form
        })
        return render(request, self.template_name, context=context)

    def post(self, request):
        form = StudentForm(request.POST)
        if form.is_valid():
            cleaned_data = form.cleaned_data
            student = Student()
            student.name = cleaned_data['name']
            student.sex = cleaned_data['sex']
            student.email = cleaned_data['email']
            student.profession = cleaned_data['profession']
            student.qq = cleaned_data['qq']
            student.phone = cleaned_data['phone']
            student.save()
            return HttpResponseRedirect(reverse('index'))
        context = self.get_context()
        context.update({
            'form': form
        })
        return render(request, self.template_name, context=context)
```

你可能已经发现了，代码量突然变多了。本来一个函数可以解决的问题，现在却有了一个类，和多一个方法。对，这么做的道理就是让每一部分变的跟明确，比如`get`就是来处理get请求，`post`就是来处理post请求。维护的时候不需要像之前那样，所有的需求都去改一个函数。

理解了这么做的原因，我们来改下urls.py的定义，完整的代码如下:

```
# coding:utf-8

from django.conf.urls import url
from django.contrib import admin

from student.views import IndexView

urlpatterns = [
    url(r'^$', IndexView.as_view(), name='index'),
    url(r'^admin/', admin.site.urls),
]
```

只是把之前的index改为了`IndexView.as_view()`，这个`as_view()`其实是对get和post方法的一个包装。里面做的事情，你可以简单的理解为我们上一节中自己写的判断`request.method`的逻辑。

## Middleware

这个需求中似乎没有需要用到Middleware的地方，不过我们可以生造一个，来练练手。

我们有这样一个需求，统计首页每次访问所消耗的时间，也就是wsgi接口或者socket接口接到请求，到最终返回的时间。先来创建一个middlewares.py的文件吧，在views.py的同级目录中。我们先来看下完整的代码：

```
# coding:utf-8
import time

from django.utils.deprecation import MiddlewareMixin
from django.urls import reverse


class TimeItMiddleware(MiddlewareMixin):
    def process_request(self, request):
        return

    def process_view(self, request, func, *args, **kwargs):
        if request.path != reverse('index'):
            return None

        start = time.time()
        response = func(request)
        costed = time.time() - start
        print('{:.2f}s'.format(costed))
        return response

    def process_exception(self, request, exception):
        pass

    def process_template_response(self, request, response):
        return response

    def process_response(self, request, response):
        return response
```

上面的代码中列出了一个Middleware的完整接口，虽然我们只用到了`process_view`。下面我们来逐个了解下:

- `process_request` - 一个请求来到middelware层，进入的第一个方法。一般情况我们可以在这里做一些校验，比如用户登录，或者HTTP中是否有认证头之类的验证。这个方法需要两种返回值，HttpResponse或者None，如果返回HttpResponse，那么接下来的处理方法只会执行`process_response`，其他的方法将不会被执行。这里**需要注意的是**，如果你的middleware在settings配置的MIDDLEWARE_CLASS的第一个的话，那么剩下的middleware也不会被执行。另外一个返回值是None，如果返回None，那么Django会继续执行其他的方法。
    
- `process_view` - 这个方法是在`process_request`之后执行的，参数如上面代码所示，其中的func就是我们将要执行的view方法，因此我们要统计一个view的执行时间，可以在这里来做。它的返回值跟`process_request`一样，HttpResponse/None，逻辑也是一样。如果返回None，那么Django会帮你执行view函数，从而得到最终的Response。
    
- `process_template_response` - 执行完上面的方法，并且Django帮忙我们执行完view之后，拿到最终的response，如果是使用了模板的Response(是指通过`return render(request, 'index.html', context={})`的方式返回Response，就会来到这个方法中。这个方法中我们可以对response做一下操作，比如`Content-Type`设置，或者其他HEADER的修改/增加。
    
- `process_response` - 当所有流程都处理完毕，就来到了这个方法，这个方法的逻辑跟`process_template_response`是完全一样的。只是`process_template_response`是针对带有模板的response的处理。
    
- `process_exception` - 上面的所有处理方法是按顺序介绍的，而这个不太一样。只有在发生异常时，才会进入到这个方法。哪个阶段发生的异常呢？可以简单的理解为在将要调用的view中出现异常（就是在`process_view`的`func`函数中）或者返回的模板Response在render时发生的异常，会进入到这个方法中。但是**需要注意的是**，如果你在`process_view`中手动调用了`func`，就像我们上面做的那样，那就不会触发`process_exception`了。这个方法接收到异常之后，可以选择处理异常，然后返回一个含有异常信息的HttpResponse，或者直接返回None，不处理，这种情况Django会使用自己的异常模板。
    

这是一层Middleware中所有方法的执行顺序和说明，那么如果有多个Middleware配置，执行顺序应该是怎样的呢？我们可以通过下面的一个图来理解下。

![django-middleware](http://django-practice-book.com/images/middleware.svg)

## TestCase

单元测试是实际开发中，很重要，但是经常被忽视的部分。原因主要是编写对于Web功能的测试所耗费的时间会高于你开发此功能的时间。因此对于需要快速开发、上线的业务来说，这个项目中关于单页测试的部分很少。

单元测试的主要目的是为了让你的代码更健壮，尤其是在进行重构或者业务增加的时候。跑通单元测试，就意味着新加入的代码，或者你修改的代码没有问题。我们在实际开发中单元测试的覆盖率是比较低，原因主要也是上面说的，写单元测试的成本，尤其是对于很复杂的业务，另外一个就是团队成员的意识。但是为了保障在业务不断扩张的同时系统的稳定，对于负责的基础的逻辑，以及整体的功能会编写测试代码。

另外一个问题是公司有没有专门的测试人员，来保障每次上线的功能都可用，进行功能上的回归测试。如果没有专门的测试人员，那单元测试，或者集成测试，就是很有必要的。即便是有专门的测试，也可以通过自动化测试来加快项目进度。从我经历过的几次线上环境的事故来看，很多细小的问题，在人工测试阶段很难被发现。所以关于单元测试，我的建议是，关键部分的单元测试一定要有，集成测试一定要有。

对于Web项目来说，单元测试是一件很复杂的事，因为它的输入输出不像一个函数那样简单。好在Django给我们提供了相对好用的测试工具。单元测试本身是一个很大的话题，在这一小节我们只演示我们现在正在开发的这个项目`学员管理系统`中如何使用单元测试。

### TestCase中几个方法的说明

在Django中运行测试用例时，如果我们用的是sqlite数据库，Django会帮我们创建一个基于内存的测试数据库，用来测试。这意味着我们测试中所创建的数据，对我们的开发环境或者线上环境是没有影响的。

但是对于MySQL数据库，Django会直接用配置的数据库用户和密码创建一个`test_student_db`的数据库，用于测试，因此需要保证有建表和建库的权限。

你也可以定义测试用的数据库的名称，通过settings配置:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'USER': 'mydatabaseuser',
        'NAME': 'mydatabase',
        'TEST': {
            'NAME': 'mytestdatabase',  ## 这里配置
        },
    },
}
```

下面对需要用到的几个方法做下说明：

- `def setUp(self)` - 如其名，用来初始化环境，包括创建初始化的数据，或者做一些其他的准备的工作。
- `def test_xxxx(self)` - 方法后面的xxxx可以是任意的东西，以`test_`开头的方法，会被认为是需要测试的方法，跑测试时会被执行。每个需要被测试的方法是相互独立的。
- `def tearDown(self)` - 跟`setUp`相对，用来清理测试环境和测试数据。在Django中，我们可以不关心这个。

### Model层测试

这一层的测试，主要是来保证数据的写入和查询是可用的，同时也需要保证我们在Model层所提供的方法是符合预期的。比如我们的Model中实现了`__unicode__`方法，保证在Python2中运行时，直接print（或者直接在web界面展示） student对象时，能看到`<Student: [name]>`这样的字样，而不是Python中的`object xxxxx`这样东西。

我们来看下代码：

```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.test import TestCase

from .models import Student


class StudentTestCase(TestCase):
    def setUp(self):
        Student.objects.create(
            name='test',
            sex=1,
            email='333@dd.com',
            profession='程序员',
            qq='3333',
            phone='32222',
        )

    def test_create_and_unicode(self):
        student = Student.objects.create(
            name='test',
            sex=1,
            email='333@dd.com',
            profession='程序员',
            qq='3333',
            phone='32222',
        )
        student_name = '<Student: test>'
        self.assertEqual(unicode(student), student_name, 'student __unicode__ must be {}'.format(student_name))

    def test_filter(self):
        students = Student.objects.filter(name='test')
        self.assertEqual(students.count(), 1, 'only one is right')
```

在`setUp`我们创建了一条数据用于测试。`test_create_and_unicode`用来测试数据创建和自定义的`__unicode__`方法有效，`test_filter`测试查询可用。

### view层测试

这一层更多的是功能上的测试，也是我们一定要写的，功能上的可用是比什么都重要的事情。当然这事你可以通过手动浏览器访问来测试，但是如果你有几百个页面呢？

这部分的测试逻辑依赖Django提供的`Django.test.Client`对象。在上面的文件中`tests.py`中，我们增加下面两个函数:

```
    def test_get_index(self):
        client = Client()
        response = client.get('/')
        self.assertEqual(response.status_code, 200, 'status code must be 200!')

    def test_post_student(self):
        client = Client()
        data = dict(
            name='test_for_post',
            sex=1,
            email='333@dd.com',
            profession='程序员',
            qq='3333',
            phone='32222',
        )
        response = client.post('/', data)
        self.assertEqual(response.status_code, 302, 'status code must be 302!')

        response = client.get('/')
        self.assertTrue(b'test_for_post' in response.content, 'response content must contain `test_for_post`')
```

`test_get_index`的作用是请求首页，并且得到正确的响应——status code = 200，`test_post_student`的作用是提交数据，然后请求首页，检查数据是否存在。

## 总结

这一部分中的三个技能点的使用，有助于你更好的理解Django，但是如果你需要更多的掌握着三个部分的内容，需要进一步的实践才行。这是我们之后要做的事了。不过关于测试部分，不仅仅是Django方面的只是，测试是一个单独的话题/领域，有兴趣的话可以找更专业的书籍来看。

小试牛刀部分就到这，其中的代码建议读者手敲一遍，在自己的Linux或者Mac上运行一下，改改代码，再次运行。别怕麻烦，也别赶进度。我经常说，所谓捷径就是一步一个脚印，每步都能前进/提高。

下一部分开始，我们将进入正式的开发阶段，请系好安全带，握紧键盘，跟上。