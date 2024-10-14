# Questions for Django Trainee at Accuknox

**Topic: Django Signals**

> **Question 1:** By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

By default, Django signals are executed synchronously. This means that when a signal is sent, all connected receiver functions are called immediately in the order they were registered, blocking the execution of further code until all receivers have completed.
```python
from django.db.models.signals import post_save
from django.dispatch import reciever

from .models import MyModel

@reciver(post_save, sender=MyModel)
def my_hander(sender, instance, **kwargs):
  print("Signal handler executed synchronously")
```

> **Question 2:** Do django signals run in the same thread as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

Django signals run in the same thread as the caller by default. This means that when a signal is emitted, its connected receiver functions are executed synchronously within the same thread context.
```
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.db import models
import threading


class MyModel(models.Model):
    name = models.CharField(max_length=100)

# Receiver function
@receiver(post_save, sender=MyModel)
def my_model_post_save(sender, instance, created, **kwargs):
    print(f"Signal received in thread: {threading.current_thread().name}")

# Emitting the signal
def save_model():
    instance = MyModel(name="Test")
    instance.save() 

save_model()
````

> **Question 3:** By default do django signals run in the same database transaction as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

By default, Django signals run in the same database transaction as the caller. This means that if a signal is sent during a transaction, the receivers of that signal will also execute within that same transaction context.
```
from django.db import models, transaction
from django.db.models.signals import post_save
from django.dispatch import receiver


class MyModel(models.Model):
    name = models.CharField(max_length=100)

# Receiver function
@receiver(post_save, sender=MyModel)
def my_model_post_save(sender, instance, created, **kwargs):
    # This operation will be part of the same transaction
    print(f"Saving related data for {instance.name}")
    RelatedModel.objects.create(my_model=instance)

class RelatedModel(models.Model):
    my_model = models.ForeignKey(MyModel, on_delete=models.CASCADE)
    description = models.CharField(max_length=100)

# Function to save model and trigger signal
def save_model():
    with transaction.atomic():  # Start a transaction block
        instance = MyModel(name="Test")
        instance.save()  # This triggers the post_save signal

# Running the save_model function
save_model()
```

## Topic: Custom Classes in Python

> **Description:** You are tasked with creating a Rectangle class with the following requirements:
1. An instance of the Rectangle class requires length:int and width:int to be initialized.
2. We can iterate over an instance of the Rectangle class 
3. When an instance of the Rectangle class is iterated over, we first get its length in the format:
 {'length': <VALUE_OF_LENGTH>} followed by the width {width: <VALUE_OF_WIDTH>}

```
class Rectangle:
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def __iter__(self):
        yield {'length': self.length}
        yield {'width': self.width}


rect = Rectangle(5, 3)

for dim in rect:
    print(dim)


### OUTPUT
{'length': 5}
{'width': 3}
###
```

