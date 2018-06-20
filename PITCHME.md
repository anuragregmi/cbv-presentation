## Class Based Views

### Django

---

@title[Views]
<span class="slide-title">Views</span>

- In Django, web pages and other content are delivered by views. Each view is represented by a simple Python function (or method, in the case of class-based views). |
- Django will choose a view by examining the URL that’s requested (to be precise, the part of the URL after the domain name) |

---
*urls.py*
```python
from .views import home_view


urlpatterns = [
  url(r'^home/$', home_view, name='home')
]
``` 


*views.py*
```python
def home_view(request, *args, **kwargs):
  return HttpResponse("This is home page")
```


---

@title[Handling Request]

<p><span class="slide-title">Handling Requests in function based views</span></p>

```python

def home_view(request, *args, **kwargs):

  if request.method == 'GET':
    # do something
    return HttpResponse(...)
    
  elif request.method == 'POST':
    # do another thing
    return HttpResponse(...)
  ...

```
---
@title[Class Based Views Base]
<p><span class="slide-title">Same thing in Class Based View</span></p>

```python
from django.views.generic import View

class HomeView(View):
  
  def get(request, *args, **kwargs):
    # do something
    return HttpResponse(...)
    
  def post(request, *args, **kwargs):
    # do another thing
    return HttpResponse(...)
    
  ...
```

---
<p><span class="slide-title">Do little change in urls.py</span></p>

```python
from .views import HomeView

urlpatterns = [
  url(r'^home/$', HomeView.as_view(), name='home')
]
```

---

- What really happened there? |
- What is there in View that we extended? |

---

**There are 4 main methods in View**
```python
as_view(cls, **initkwargs)

dispatch(self, request, *args, **kwargs)

http_method_not_allowed(self, request, *args, **kwargs)

options(self, request, *args, **kwargs)
```

---
```python
as_view(cls, **initkwargs)
```

- Main entry point for a request-response process. |
- Sets request, args, kwargs attributes of the view. |
- Calls dispatch |

---
```python
dispatch(self, request, *args, **kwargs)
```

- Try to dispatch to the right method.|
- if a method doesn't exist, defer to the error handler. Also defer to the error handler if the request method isn't on the approved list.|

---

```python
http_method_not_allowed(self, request, *args, **kwargs)
```

- called by dispatch if requested method is not allowed. |
- Returns HttpResponseNotAllowed |

---
```python
options(self, request, *args, **kwargs)
```

- Handle responding to requests for the OPTIONS HTTP verb.|
- The HTTP OPTIONS method is used to describe the communication options for the target resource.|
---

# What now?
- Let's render some templates |

--- 
*views.py*
```python
from django.shortcuts import render

def home_page(request, *args, **kwargs):
  
  context = {
    'name': 'Aayulogic',
    'address': 'Minbhawan'
  }
  
  return render(request, 'home_page.html', context)
```
---
*home_page.html*
```html+jinja
<!DOCTYPE html>
<head>
  <title>{{ name }}</title>
</head>
<body>
  <h1> {{ name }} </h1>
  <h2> {{ address }} </h2>
</body>
</html>
```
---
#### Let's do that in Class Based View
```python
from django.views.generic import View

class HomeView(View):
  
  def get(request, *args, **kwargs):
    context = {
      'name': 'Aayulogic',
      'address': 'Minbhawan'
    }
  
    return render(request, 'home_page.html', context)

```
---
## There's is a better way
---
```python
from django.views.generic.base import TemplateView

class HomeView(TemplateView)
    template_name = "home_page.html"

    def get_context_data(self, **kwargs):
        
        context = super().get_context_data(**kwargs)
        
        context['name'] = 'Aayulogic'
        context['address'] = 'Minbhawan'
        
        return context
```
---
**What is in that TemplateView ?**
- TemplateView extends three classes
  - ContextMixin
  - TemplateResponseMixin
  - View
---
<p><span class="slide-title">ContextMixin</span></p>
```python
extra_context = None

get_context_data(self, **kwargs)
```
---
<p><span class="slide-title">TemplateResponseMixin</span></p>
```python
content_type = None 
response_class = <class 'django.template.response.TemplateResponse'> 	TemplateResponseMixin
template_engine = None
template_name = None

get_template_names(self)

render_to_response(self, context, **response_kwargs)
```
---
<p><span class="slide-title">TemplateResponseMixin</span></p>

```python

def get(self, request, *args, **kwargs):
    context = self.get_context_data(**kwargs)
    return self.render_to_response(context)

```
