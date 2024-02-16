<H1 align="center">Estrutura Autenticação</H1>
<p align="center">🚀 Projeto de criação de uma estrutura de autenticação utilizando Django para referências futuras</p>

## Recursos Utilizados

* Django 5.0.2
* Python 3.10


## Criação do todo_list

Projeto inicial criado com estrutura principal, alterando urls.py para adicionar base.urls para adicionar os packages. 
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



## Criação da base

Criação de um pacote que será responsável pela lógica da autenticação.

 ```
python manage.py startapp base
 ```

 
### urls.py

Possuí o mapeamento das rotas.

Os caminhos e as respectivas views que serão renderizados utilizando Classes como Views.

```
    path('login/', CustomLoginView.as_view(), name='login'),
    path('logout/', LogoutView.as_view(next_page='login'), name='logout'),
    path('register/', RegisterPage.as_view(), name='register'),
```

#### LoginView

O caminho para achar o arquivo html será passado através do atributo template_name de CustomLoginView herdando as propriedades da Classe LoginView e a modificando em views.py.
```
path('login/', CustomLoginView.as_view(), name='login'),
```

#### LogoutView

Essa view importará a Classe LogoutView de forma nativa e utilizará seu atributo "next-page='login'" para redirecionar para página de login no caso do usuário encerrar a sessão.
```
path('logout/', LogoutView.as_view(next_page='login'), name='logout'),
```

#### RegisterPage

O caminho para achar o arquivo html será passado através do atributo template_name de RegisterPage herdando as propriedades da Classe FormView e a modificando em views.py.
```
path('register/', RegisterPage.as_view(), name='register'),
```


## Criação Template

Diretório responsável por armazenar as páginas htmls que serão renderizadas.

Por convenção, dentro de "template" é utilizado nome_do_projeto/nome_da_view.html para que framework reconheça o caminho.
 

## Views.py
Responsável pelo controller de renderização de views e fluxo de dados.




### CustomLoginView(LoginView)

Herdando a classe LoginView e modificando seus atributos, demonstrando uma das vantagens da utilização de classes como Views.
```
template_name = 'base/login.html'
fields = '__all__'
redirect_authenticated_user = True

def get_success_url(self):
  return reverse_lazy('tasks')
```



Alterando template_name para renderização de um caminho personalizado para achar o arquivo html.
```
template_name = 'base/login.html'
```

Utilizando o formulario nativo de LoginView passando todos os seus campos para renderizar no arquivo html.
```
fields = '__all__'
```

Se o usuário estiver autenticado, redireciona-lo para o caminho especificado.
```
redirect_authenticated_user = True
```

Alterando um método base de LoginView que foi herdado para CustomLoginView e caso o login for efetuado ser redirecionado para o caminho 'tasks'.
```
def get_success_url(self):
  return reverse_lazy('tasks')
```




### RegisterPage(FormView)

Herdando a classe FormView e modificando seus atributos.

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

Alterando template_name para renderização de um caminho personalizado para achar o arquivo html.
```
template_name = 'base/register.html'
```

Utilizando o formulario nativo de FormView passando todos os seus campos para renderizar no arquivo html.
```
form_class = UserCreationForm
```

Se o usuário estiver autenticado, redireciona-lo para o caminho especificado.
```
redirect_authenticated_user = True
```


Se o registro for efetuado com sucesso ser redirecionado para o caminho 'tasks'.
```
success_url = reverse_lazy('tasks')
```


Reescrevendo um método nativo, caso o formulário for válido armazenar em user e se o usuario não for None significando que foi registrado então efetuar o login, caso contrário utilizar o método nativo super da classe FormView e redirecionando para RegisterPage novamente.
```
def form_valid(self, form):
    user = form.save()
    if user is not None:
        login(self.request, user)
    return super(RegisterPage, self).form_valid(form)
```

Reescrevendo um método nativo, se o usuário ja estiver autenticado e tentar entrar nesse caminho de registro será redirecionado para o caminho 'tasks'.
```
def get(self, *args, **kwargs):
    if self.request.user.is_authenticated:
        return redirect('tasks')
    return super(RegisterPage, self).get(*args, **kwargs)
```



### TaskCreate(LoginRequiredMixin, CreateView):
Herdando a classe LoginRequiredMixin como forma de verificação de autenticação e CreateView para renderização da View e modificando seus atributos.


Reescrevendo um método nativo, atribuindo o usuário logado no registro da task, assim usuário X não poderá registrar uma task como usuário Y.
```
 def form_valid(self, form): 
        form.instance.user = self.request.user
        return super(TaskCreate, self).form_valid(form)
```

### DeleteView(LoginRequiredMixin, DeleteView):
Herdando a classe LoginRequiredMixin como forma de verificação de autenticação e DeleteView para renderização da View e modificando seus atributos.


Reescrevendo um método nativo.
```
def get_queryset(self):
        owner = self.request.user
        return self.model.objects.filter(user=owner)
```

# Resultado

## Login
<img src="https://cdn.discordapp.com/attachments/1046824853015113789/1208151548165890058/image.png?ex=65e23dcf&is=65cfc8cf&hm=68d7841323021166cad6330bcbb95a45e9b9a0907f8d52aca434e5e748e90933&" alt="">

## Registro
<img src="https://cdn.discordapp.com/attachments/1046824853015113789/1208151471972155393/image.png?ex=65e23dbd&is=65cfc8bd&hm=b620949867bf8294ad4e078d341de6175f7a6e7e05c1199ba436fc32fe262b94&" alt="">

## Index
<img src="https://cdn.discordapp.com/attachments/1046824853015113789/1208150467717373992/image.png?ex=65e23ccd&is=65cfc7cd&hm=a951fafb8228fce5f396df0a77a65e524f07b121dd215e8d62aa937f109c2ec0&" alt="">



