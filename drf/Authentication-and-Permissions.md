#5. Authentication & Permissions

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

- add users in serializers.py
- Because the relationship of the User model and snippets is 'reverse', snippets doesn't include automatically when we use Modelserializer class. So have to add fields explicitly.

```py
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())

    class Meta:
        model = User
        fields = ['id', 'username', 'snippets']
```

- modify views.py
- To only use read-only views, use ListAPIView & RetrieveAPIView of generic



