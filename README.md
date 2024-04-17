<H1 align="center">Authentication</H1>
<p align="center">🚀 Project to create an authentication framework using Django for future references</p>

## Resources Used

* Django 5.0.2
* Python 3.10


<div align="center">
  <h3> Login </h3>
  <img src="https://github.com/lucasmargui/Django_Projeto_Autenticacao/assets/157809964/da012078-075e-4e77-befb-db40d2be57f5" style="width:70%">
  <h3> Register </h3>
  <img src="https://github.com/lucasmargui/Django_Projeto_Autenticacao/assets/157809964/302b1fd5-694f-432b-9bb0-08063056be1e" style="width:70%">
  <h3> Index </h3>
  <img src="https://github.com/lucasmargui/Django_Projeto_Autenticacao/assets/157809964/718bd04c-21c8-45cb-adb4-39c6e1575147" style="width:70%">
</div>


## Create todo_list

<details>
 <summary>Click to show content</summary>

Initial project created with main structure, changing urls.py to add base.urls to add the packages.
 ```
django-admin startproject todo_list
 ```


### urls.py

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
 path('admin/', admin.site.urls),
 path('', include('base.urls')),
]
```

### settings.py

```
INSTALLED_APPS = [
 'base.apps.BaseConfig',
 'django.contrib.admin',
 'django.contrib.auth',
 'django.contrib.contenttypes',
 'django.contrib.sessions',
 'django.contrib.messages',
 'django.contrib.staticfiles',
]
```



## Create base app

Creation of a package that will be responsible for the authentication logic.

 ```
python manage.py startapp base
 ```


### urls.py

I have the route mapping.

The paths and respective views that will be rendered using Classes as Views.

```
 path('login/', CustomLoginView.as_view(), name='login'),
 path('logout/', LogoutView.as_view(next_page='login'), name='logout'),
 path('register/', RegisterPage.as_view(), name='register'),
```

#### LoginView

The path to find the html file will be passed through the template_name attribute of CustomLoginView, inheriting the properties of the LoginView Class and modifying it in views.py.
```
path('login/', CustomLoginView.as_view(), name='login'),
```

#### LogoutView

This view will import the LogoutView Class natively and will use its "next-page='login'" attribute to redirect to the login page if the user closes the session.
```
path('logout/', LogoutView.as_view(next_page='login'), name='logout'),
```

#### RegisterPage

The path to find the html file will be passed through the template_name attribute of RegisterPage, inheriting the properties of the FormView Class and modifying it in views.py.
```
path('register/', RegisterPage.as_view(), name='register'),
```

</details>



## Template Creation

<details>
 <summary>Click to show content</summary>

Directory responsible for storing the html pages that will be rendered.

By convention, within "template" project_name/view_name.html is used so that the framework recognizes the path.

</details>





## Views.py

<details>
 <summary>Click to show content</summary>

Responsible for the view rendering and data flow controller.


### CustomLoginView(LoginView)

Inheriting the LoginView class and modifying its attributes, demonstrating one of the advantages of using classes like Views.
```
template_name = 'base/login.html'
fields = '__all__'
redirect_authenticated_user = True

def get_success_url(self):
 return reverse_lazy('tasks')
```



Changing template_name to render a custom path to find the html file.
```
template_name = 'base/login.html'
```

Using the native LoginView form, passing all its fields to render in the html file.
```
fields = '__all__'
```

If the user is authenticated, redirect him to the specified path.
```
redirect_authenticated_user = True
```

Changing a LoginView base method that was inherited to CustomLoginView and if the login is performed, it will be redirected to the 'tasks' path.
```
def get_success_url(self):
 return reverse_lazy('tasks')
```




### RegisterPage(FormView)

Inheriting the FormView class and modifying its attributes.

```
template_name = 'base/register.html'
form_class = UserCreationForm
redirect_authenticated_user = True
success_url = reverse_lazy('tasks')

def form_valid(self, form):
 user = form.save()
 if user is not None:
 login(self.request, user)
 return super(RegisterPage, self).form_valid(form)

def get(self, *args, **kwargs):
 if self.request.user.is_authenticated:
 return redirect('tasks')
 return super(RegisterPage, self).get(*args, **kwargs)
```

Changing template_name to render a custom path to find the html file.
```
template_name = 'base/register.html'
```

Using the native FormView form, passing all its fields to render in the html file.
```
form_class = UserCreationForm
```

If the user is authenticated, redirect him to the specified path.
```
redirect_authenticated_user = True
```


If registration is successful, you will be redirected to the 'tasks' path.
```
success_url = reverse_lazy('tasks')
```


Rewriting a native method, if the form is valid to store in user and if the user is not None meaning that it was registered then log in, otherwise use the native super method of the FormView class and redirecting to RegisterPage again.
```
def form_valid(self, form):
 user = form.save()
 if user is not None:
 login(self.request, user)
 return super(RegisterPage, self).form_valid(form)
```

Rewriting a native method, if the user is already authenticated and tries to enter this registry path, they will be redirected to the 'tasks' path.
```
def get(self, *args, **kwargs):
 if self.request.user.is_authenticated:
 return redirect('tasks')
 return super(RegisterPage, self).get(*args, **kwargs)
```



### TaskCreate(LoginRequiredMixin, CreateView):
Inheriting the LoginRequiredMixin class as a way of checking authentication and CreateView for rendering the View and modifying its attributes.


Rewriting a native method, assigning the logged in user to the task registration, so user X will not be able to register a task as user Y.
```
 def form_valid(self, form):
 form.instance.user = self.request.user
 return super(TaskCreate, self).form_valid(form)
```

### DeleteView(LoginRequiredMixin, DeleteView):
Inheriting the LoginRequiredMixin class as a way of checking authentication and DeleteView for rendering the View and modifying its attributes.


Rewriting a native method.
```
def get_queryset(self):
 owner = self.request.user
 return self.model.objects.filter(user=owner)
```

</details>








