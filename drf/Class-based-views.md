# 4.Class-based Views

## Basic Class-based view
- Separate HTTP method more clearly
- rest_framework.views.APIView 상속

```py
# snippets/views.py
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from django.http import Http404
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status


class SnippetList(APIView):
    """
    List all snippets, or create a new snippet.
    """
    def get(self, request, format=None):
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
        
        
class SnippetDetail(APIView):
    """
    Retrieve, update or delete a snippet instance.
    """
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk, format=None):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

- 클래스형 뷰 이므로 urls.py에서 as_view()함수로 클래스 인스턴스를 생성하고, 인스턴스의 dispatch() 메소드를 호출. dispatch()메소드는 요청을 검사해 GET ,POST 등의 어떤 HTTP method로 요청되었는지 알아낸 후 인스턴스 내에서 해당 이름을 갖는 메소드로 요청을 중계해준다. 만일 해당 메소드가 정의되어있지 않으면 HttpResponseNotAllowed 익셉션을 발생시킨다.

- SnippetList, SnippetDetail 클래스는 view클래스를 상속받고 있고, View클래스에는 as_view()함수와, dispatch() 메소드가 정의되어 있어 클래스에 따로 정의하지 않아도 사용 가능하다.

```py
# snippets/urls.py
from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from snippets import views

urlpatterns = [
    path('snippets/', views.SnippetList.as_view()),
    path('snippets/<int:pk>/', views.SnippetDetail.as_view()),
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

## Using Mixins
- To increase reuse
- CRUD has already been implemented

SnippetList class

	- GenericAPIView + (ListModelMixin + CreateModelMixin)
	- use list(), create() functions

```py
from rest_framework import mixins
from rest_framework import generics

from snippets.models import Snippet
from snippets.serializers import SnippetSerializer


class SnippetList(  mixins.ListModelMixin,
                    mixins.CreateModelMixin,
                    generics.GenericAPIView  ):
    """
    List all code snippets, or create a new snippet.
    """
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer


    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```

SnippetDetail class

	- GenericAPIView + (RetrieveModelMixin + UpdateModelMixin, DestroyModelMixin)
	- use retrieve(), update(), destroy() functions

```py
class SnippetDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

## Using Generic

- mix & use Mixins
- simpler code
- don't need return

```py
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import generics


class SnippetList(generics.ListCreateAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer


class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
``` 
