# Questions for Django Trainee at Accuknox

Topic: Django Signals

Question 1: By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.
'''
from django.db.models.signals import post_save
from django.dispatch import reciever

from .models import MyModel

@reciver(post_save, sender=MyModel)
def my_hander(sender, instance, **kwargs):
  print("Signal handler executed synchronously")
  '''
