**Question 1:** By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

- **By default, Django Signals are executed synchronously**

```
import time
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def my_handler(sender, **kwargs):
    print("Signal handler started...")
    time.sleep(5)
    print("Signal handler finished.")

user = User.objects.create(username='test_user')
```

</br>

**Question 2:** Do django signals run in the same thread as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

- **Yes**

```
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def user_created_signal(sender, instance, **kwargs):
    print(f"Signal executed in thread: {threading.current_thread().name}")

print(f"Main thread: {threading.current_thread().name}")
User.objects.create(username="test_user")
```

</br>

**Question 3:** By default do django signals run in the same database transaction as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

- **Yes**

```
from django.db import transaction
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from django.core.exceptions import ObjectDoesNotExist

@receiver(post_save, sender=User)
def user_created_signal(sender, instance, **kwargs):
    try:
        user = User.objects.get(username=instance.username)
        print("Signal in transaction, User exists.")
    except ObjectDoesNotExist:
        print("Signal in transaction, User does not exist.")

with transaction.atomic():
    User.objects.create(username="test_user")
```
