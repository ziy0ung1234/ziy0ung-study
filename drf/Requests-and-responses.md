# 3. Requests and Responses

## Request object
- HttpRequest's extended version
- λ μ μ°ν request parsing
- π¨ <span style="color:red"> request.data </span> π¨
 request.POSTμ μ μ¬νμ§λ§ μ’λ ν¨μ¨μ μ΄λ€
	- request.POST : only handeling form data, only use POST method
	- request.data : more data, can use POST,PUT,PATCH method

## Response object
- clientκ° μμ²­ν νμμΌλ‘ render

## Status codes
- μ’ λ λͺννκ² μ λ¬νκΈ° μν΄ μ§μ  μ«μλ₯Ό μ°μ§ μκ³  DRFμμ μ κ³΅νλ λ©μΈμ§ μ¬μ©
	ex) HTTP_400_BAD_REQUEST

## Wrapping API Views
- @api_view decorator : Function based view
- APIView class : class based view
- μ’λ νμ€νκ³  λλ§μ μ»¨νμΈ λ€μ add νλ ν¨κ³Ό
- μ μ νκ² μλ¬ λ©μΈμ§λ₯Ό response 
- μλͺ»λ μλ ₯μ λν΄ ParseError exception λ°μμν΄

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

### url format μ λ―Έμ¬ μΆκ°
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

