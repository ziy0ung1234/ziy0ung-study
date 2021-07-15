# 3. Requests and Responses

## Request object
- HttpRequest's extended version
- 더 유연한 request parsing
- 🚨 <span style="color:red"> request.data </span> 🚨
 request.POST와 유사하지만 좀더 효율적이다
	- request.POST : only handeling form data, only use POST method
	- request.data : more data, can use POST,PUT,PATCH method

## Response object
- client가 요청한 타입으로 render

## Status codes
- 좀 더 명확하게 전달하기 위해 직접 숫자를 쓰지 않고 DRF에서 제공하는 메세지 사용
	ex) HTTP_400_BAD_REQUEST

## Wrapping API Views
- @api_view decorator : Function based view
- APIView class : class based view
- 좀더 확실하고 더많은 컨텐츠들을 add 하는 효과
- 적절하게 에러 메세지를 response 
- 잘못된 입력에 대해 ParseError exception 발생시킴

### Modifying Views

- 1) add decorator
- 2) JsonResponse -> Response
- 3) parsing request directly -> request.data
- 4) modify status code

```py
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer


@api_view(['GET', 'POST'])
def snippetList(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.data, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'DELETE'])
def snippetDetail(request, pk):
    """
    Retrieve, update or delete a code snippet.
    """
    try:
        snippet = Snippet.object.get(pk=pk)
    except Snippet.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

### url format 접미사 추가
ex) http://example.com/api/items/4.json

- add arguments to the view func

```py
def snippetList(request, format=None):
def snippetDetail(request, pk, format=None):
```

- modifying urls.py
```py
from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from snippets import views

urlpatterns = [
    path('snippets/', views.snippet_list),
    path('snippets/<int:pk>', views.snippet_detail),
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

