# 5. Authentication & Permissions

Currently, anyone can modify or delete code snippets, So need to add a few things.

1) add creators in code snippets
2) only authenticated users can create snippets
3) only creators update and delete snippets
4) Unauthenticated requests are granted read-only.

## Add a owner in themodels.py

```py
# snippets/models.py
from pygments.lexers import get_lexer_by_name
from pygments.formatters.html import HtmlFormatter
from pygments import highlight

class Snippet(models.Model):
    """생략"""
    def save(self, *args, **kwargs):
        lexer = get_lexer_by_name(self.language)
        linenos = 'table' if self.linenos else False
        options = {'title': self.title} if self.title else {}
        formatter = HtmlFormatter(style=self.style, linenos=linenos,
                                  full=True, **options)
        self.highlighted = highlight(self.code, lexer, formatter)
        super(Snippet, self).save(*args, **kwargs)
    """생략"""
```

For the purpose of this tutorial,  delete DB and create again
```rm -f db.sqlite3
rm -r snippets/migrations
python manage.py makemigrations snippets
python manage.py migrate

python manage.py createsuperuser
```

## Add User model Endpoint

### add users in serializers.py
- Because the relationship of the User model and snippets is 'reverse', snippets is not include automatically when we use Modelserializer class. So have to add fields explicitly.

```py
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())

    class Meta:
        model = User
        fields = ['id', 'username', 'snippets']
```

### modifying views.py
- To only use read-only views, use ListAPIView & RetrieveAPIView of generic

### modifying snippets/urls.py

```py
path('users/', views.UserList.as_view()),
path('users/<int:pk>/', views.UserDetail.as_view()),
```

## Connecting Snippets and Users

When code snippet is created, it is not yet associated with the user. The user is not automatically serialized yet, but it comes in as a request attribute.

This can be solved by overriding the `.perform_create()` method in snippet view. This allows you to manage how the instance will be stored and clarify the information comming into the request.

### Adding some codes in SnippetList view class

```py
def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
```

Now serializer's `create()` method will forward the owner field with the validated data.

### Modifying the serializer

Now the snippet and the user who made it are connected. This should be reflected in the Snippet Serializer, also in the meta class too

```py
owner = serializers.ReadOnlyField(source='owner.username')
```

The source argument can control attributes used to fill in the field and point to all attributes in serialized instance.
Also, It can use dot`.`notation as shown above, which is similar to Django's templatet language.

Fields added ard ReadOnlyField classes with no type, unlike other field types such as CharField and BooleanField.
Unformatted ReadOnlyField is always read-only and used in serialized things. However, because of read-only, it cannot be used to update model instance when deserialized.

### Adding required permissins to views

The code snippet is now connected with the user, and only authenticated users should be allowed to create/update/delete code snippets.

The REST framework contains several permission classes that will be used to restrict who can access a given view. In this case, the IsAuthenticatedOrReadOnly class allows authorized requests to read/write and unauthenticated requests to read only.

I first import the permission class to the view moduls, and then add the following attributes to the SnippetList and SnippetDetail view class.
```py
#snippets/views.py
from rest_framework import permissions

permission_classes = [permissions.IsAuthenticatedOrReadOnly]
```
### Adding login to the Browseable API

Once you open a browser and access the API, you cannot create a new code snippet. To solce this problem, the user login func is required. Add login view to the API and modify the URLconf in urls.py.

In urlpattern, 'api-auth' is the part that actually becomes the URL you want to use.

Now, you can access the browser and refresh to see the Login display in the upper right corner. If you log in as a previously created user, you can create code snippets again.

After creating several code snippets and navigating to the '/users/' endpoint, a list of id's user-created snippets will be shown in each user's 'snippets' field.

### Object level permissons

A list of all code snippets should be visible to anyone, but only thos wog create the code snippets should be allowed to modify and delete them.

Create a permissons.py file inside the snippet app.

When tbe browser is re-run, code snippet's "DELETE" and "PUT" action will only appear to the user who created them.

### Authenticating with the API

Now that we have established permissions for the API, we need authentication for requests in order to modify code snippets.

The default values of Session Authentication and Basic Authentication are currently set because no authentication class has been set.

You can log in when using the API via a web browser, and the browser's session provides the authentication required for request.

When using APIs through programming methods, we need to explicitly write authentication credentials for each request. If a snippet is created without authentication information, the following error occurs.

```
http POST http://127.0.0.1:8000/snippets/ code="print(123)"

{
    "detail": "Authentication credentials were not provided."
}
```

A successful request is possible if the request includes the user name and password that have been created in advance.

```
http -a admin:password123 POST http://127.0.0.1:8000/snippets/ code="print(789)"

{
    "id": 1,
    "owner": "admin",
    "title": "foo",
    "code": "print(789)",
    "linenos": false,
    "language": "python",
    "style": "friendly"
}
```
