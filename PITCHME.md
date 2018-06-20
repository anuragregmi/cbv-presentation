## Class Based Views

### Django

---

@title[Function Based Views]
<span class="slide-title">Function Based Views</span>

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
|


*views.py*
```python
def home_view(request, *args, **kwargs):
  return HttpResponse("This is home page")
```
|

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

- What really happened there?
- What is there in View that we extended?

---

**There are 4 main methods in View**
````python
as_view(cls, **initkwargs)
dispatch(self, request, *args, **kwargs)
http_method_not_allowed(self, request, *args, **kwargs)
options()
```

---


