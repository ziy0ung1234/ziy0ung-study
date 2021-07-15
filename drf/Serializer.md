# 2. Serializer
serializers는 쿼리셋들 및 모델 인스턴스와 같은 복잡한 데이터를 `JSON`,`XML`또는 기타 컨텐츠 유형으로 쉽게 렌더링 할 수 있는 Python 기본 데이터 유형으로 변환해 줍니다. 또한 serializer는 deserialization을 제공하여, 들어오는 데이터의 유효성을 처음 확인한 후 구문 분석 된 데이터를 복합 형식으로 다시 변환 할 수 있습니다.

## Django에서 클라이언트로 복잡한 데이터(모델 인스턴스등)를 보내려면 `string(문자열)`타입으로 변환해야하는데 이 변환을 `serializer`라고 한다. 반대로 클라이언트의 문자열 타입 데이터를 django로 받을 때 python 기본 데이터 유형으로 받아야 하는데 이 변환을 `deserializer` 라고 한다.

REST framework는 `ModelSerializer`(모델 인스턴스와 쿼리셋을 다루는 시리얼라이저를 생성하기 유용한 클래스) 뿐만 아니라 응답의 출력을 제어하는 강력하고 일반적인 방법을 제공하는 `Serializer`클래스를 제공한다.

## Difference of ModelSerializer & Serializer

- serializer를 만들때 모델을 다시 한번 작성하는 것처럼 각각의 필드및 속성들을 정의해주면서 필드의 내용들이 모델과 중복되는 경우가 생겼고, `create()` 메소드와 `update()` 메소드를 직접 정의해야 하는 불편함 덜어줄 수 있는 것이 ModelSerializer이다.

### 3 functions of ModelSerializers
- (의존하고 있는 모델에 기반해서) Serializer 필드를 자동으로 생성
- Serializer를 위한 validator 제공 : ex) unique_together_validators
- .create(), .update() 함수 기본으로 제공하여 다시 만들 필요 없음 

## Example

### Declaring Models

```py
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted([(item, item) for item in get_all_styles()])


class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ['created']
```

모델을 만들었으니 makemigrations -> migrate 해줌

### Declaring Serializer

```py
# snippets/serializers.py
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```

- 유효성이 검사 된 데이터를 기반으로 완전한 객체 인스턴스를 반환하려면, `create()`,`update()` 메소드 중 하나나 둘 모두를 구현해야 한다. 객체 인스턴스가 django 모델과 일치하는 경우 이 메소드가 객체를 데이터베이스에 저장하도록 해야한다. 데이터를 역직렬화할때 `.save()`를 호출하여 유효성이 검사된 데이터를 기반으로 객체 인스턴스를 반환할 수 있다.

- `.save()`를 호출하면 serializer 클래스를 인스턴스화 할 때 기존 인스턴스가 전달 되었는지 여부에 따라 새 인스턴스를 만들거나 기존 인스턴스를 업데이트

### Use in the Django shell

- 인스턴스 생성
```py
>>> from snippets.models import Snippet
>>> from snippets.serializers import SnippetSerializer
>>> from rest_framework.renderers import JSONRenderer
>>> from rest_framework.parsers import JSONParser

snippet = Snippet(code='foo = "bar"\n')
snippet.save()

snippet = Snippet(code='print("hello, world")\n')
snippet.save()
```

- 직렬화 (instance to json)
```py
>>> serializer = SnippetSerializer(snippet)
>>> serializer.data
{'id': 4, 'title': '', 'code': 'print("hello, world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}
```

- json 변환
```py
>>> content = JSONRenderer().render(serializer.data)
>>> content
b'{"id":4,"title":"","code":"print(\\"hello, world\\")\\n","linenos":false,"language":"python","style":"friendly"}'
```

- 역직렬화 (json to instance)
```py
import io

>>> stream = io.BytesIO(content)
>>> data = JSONParser().parse(stream)
>>> serializer = SnippetSerializer(data=data)
>>> serializer.is_valid()
True
>>> serializer.validated_data
OrderedDict([('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])
>>> serializer.save()
<Snippet: Snippet object (5)>
```

데이터를 deserializer(역직렬화) 할 때 유효성이 검사 된 데이터에 액서스하기 전에 항상 `is_valid()`를 호출하거나 객체 인스턴스를 저장해야 한다. 
유효성 검사 오류 발생시, `.errors속성에는 결과 오류 메세지(dict) 

```py
>>> serializer = SnippetSerializer(data={'title': 'ggg', 'code': ''})
>>> serializer.is_valid()
False
>>> serializer.errors
{'code': [ErrorDetail(string='This field may not be blank.', code='blank')]}

```
- + 쿼리셋 직렬화
	- many=True 플래그 추가
```py
>>> serializer = SnippetSerializer(Snippet.objects.all(), many=True)
>>> serializer.data
[OrderedDict([('id', 1), ('title', ''), ('code', 'foo = "bar"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 2), ('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 3), ('title', ''), ('code', 'foo = "bar"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 4), ('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 5), ('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])]
```
### ModelSerializer

- Modifying serializers
```py
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = ['id', 'title', 'code', 'linenos', 'language', 'style']
```

- check in the shell
```py
>>> from snippets.serializers import SnippetSerializer
>>> serializer = SnippetSerializer()
>>> print(repr(serializer))
SnippetSerializer():
    id = IntegerField(read_only=True)
    title = CharField(allow_blank=True, max_length=100, required=False)
    code = CharField(style={'base_template': 'textarea.html'})
    linenos = BooleanField(required=False)
    language = ChoiceField(choices=[('abap', 'ABAP'), ('abnf', 'ABNF')...
    style = ChoiceField(choices=[('autumn', 'autumn'), ('borland', 'borland'), ...
```

### Declaring Views

- Read, Create
	- GET : Show existing snippets
	- POST : Create new snippets

```py
from django.http import HttpResponse, JsonResponse
from rest_framework.parsers import JSONParser
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer

def snippetList(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return JsonResponse(serializer.data, safe=False)

    elif request.method == 'POST':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer, status=201)
        return JsonResponse(serializer, status=400)
```

- Read, Update, Delete

```py
def snippetDetail(request, pk):
    """
    Retrieve, update or delete a code snippet.
    """
    try:
        snippet = Snippet.object.get(pk=pk)
    except:
        return HttpResponse(status=404)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return JsonResponse(serializer.data)

    elif request.method == 'PUT':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(snippet, data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data)
        return JsonResponse(serializer.errors, status=400)

    elif request.method == 'DELETE':
        snippet.delete()
        return HttpResponse(status=204)
```
- setting urls.py

```py
# snippets/urls.py
from django.urls import path
from snippets import views

urlpatterns = [
    path('snippets/', views.snippetList),
    path('snippets/<int:pk>/', views.snippetDetail),
]
# tutorial/urls.py
from django.urls import path, include

urlpatterns = [
    path('', include('snippets.urls')),
]
```


