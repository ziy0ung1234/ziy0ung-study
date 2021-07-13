# 2. Serializer

serializers는 쿼리셋들 및 모델 인스턴스와 같은 복잡한 데이터를 `JSON`,`XML`또는 기타 컨텐츠 유형으로 쉽게 렌더링 할 수 있는 Python 기본 데이터 유형으로 변환해 줍니다. 또한 serializer는 deserialization을 제공하여, 들어오는 데이터의 유효성을 처음 확인한 후 구문 분석 된 데이터를 복합 형식으로 다시 변환 할 수 있습니다.

## Django에서 클라이언트로 복잡한 데이터(모델 인스턴스등)를 보내려면 `string(문자열)`타입으로 변환해야하는데 이 변환을 `serializer`라고 한다. 반대로 클라이언트의 문자열 타입 데이터를 django로 받을 때 python 기본 데이터 유형으로 받아야 하는데 이 변환을 `deserializer` 라고 한다.

REST framework는 `ModelSerializer`(모델 인스턴스와 쿼리셋을 다루는 시리얼라이저를 생성하기 유용한 클래스) 뿐만 아니라 응답의 출력을 제어하는 강력하고 일반적인 방법을 제공하는 `Serializer`클래스를 제공한다.

## Difference of ModelSerializer & Serializer

## Example

### Declaring Serializers

예제를 위해 사용할 간단한 객체를 만들어야 한다.
```py
#models.py
from datetime import datetime

class Comment(object):
    def __init__(self, email, content, created=None):
        self.email = email
        self.content = content
        self.created = created or datetime.now()

comment = Comment(email='leila@example.com', content='foo bar')
```
comment 객체에 해당하는 데이터를 serializer 및 deserializer화하는데 사용할 수 있는 serializer를 선언한다.

serializer를 선언하면 django의 form을 선언하는 것과 유사하다.

### Serializing objects

```py
#serializers.py
from rest_framework import serializers

class CommentSerializer(serializers.Serializer):
    email = serializers.EmailField()
    content = serializers.CharField(max_length=200)
    created = serializers.DateTimeField()
```

CommentSerializer를 사용해 주석 또는 주석 목록을 직렬화 할 수 있다. 즉, `Serializer` 클래스를 사용하는 것은 `Form` 클래스를 사용하는 것과 비슷하다.

```py
serializer = CommentSerializer(comment)
serializer.data
# {'email': 'leila@example.com', 'content': 'foo bar', 'created': '2016-01-27T15:17:10.375877'}
```
모델 인스턴스를 파이썬 기본 데이처 유형으로 변환. serializer 과정을 마무리하기 위해 데이터를 `JSON`으로 렌더링한다.

```py
from rest_framework.renderers import JSONRenderer

json = JSONRenderer().render(serializer.data)
json
# b'{"email":"leila@example.com","content":"foo bar","created":"2016-01-27T15:17:10.375877"}'
```
