# django model field

 ```
 Primary Key : AutoField, BigAutoField
 문자열(str) : CharField, TextField, SlugField
 날짜/시간 : DateField, TimeField, DateTimeField, DurationField
 참/거짓 : BooleanField, NullBooleanField
 숫자 : IntegerField, SmallIntegerField, PositiveIntegerField, PositiveSmallIntegerField, BigIntegerField, DecimalField, FloatField
 파일 : BinaryField, FileField, ImageField, FilePathField
 ```

```
e-mail : EmailField
URL : URLField
UUID : UUIDField
IP : GenericIPAddressField
```

> 이외, 다양한 커스텀 필드 : https://django-model-utils.readthedocs.io/en/latest/index.html

## 관계를 표현하는 모델 필드

1. ForeignKey

* 1:N 관계에서 N 측에 명시

```
# to : 대상모델(1측) , on_delete : record(대상모델) 삭제 시 rule 
ForeignKey(to, on_delete)
```

> limit_choices_to 옵션을 통해 선택항목 제한이 가능

* FK에서의 reverse_name (reverse 접근 시 속성명)

"모델명소문자_set"

> 만약 대상모델이 post이고 N 측이 comment 이면, "post.comment_set.all()" 를 통해 접근 가능

> application 별로 모델 명이 동일하면, 이름에 대한 충돌이 일어나므로 변경해주어야 함

2. OneToOneField

* 1:1 관계에서 사용하며, 어느 쪽에서 지정해도 무관

```
OneToOneField(to, on_delete)
```

* reverse 접근 시 속성명

"모델명소문자"

3. ManyToManyField

* M:N 관계에서 어느 쪽이라도 필드 지정 가능

```
ManyToManyField(to, blank=False)
```

## model field들은 DB field type을 반영

* DB에서 지원하는 필드들을 반영

```
Varchar field type -> CharField, SlugField, URLField, EmailField
```

* python data type과 DB data type을 mapping

```
AutoField -> int
BinaryField -> bytes
BooleanField -> bool
CharField/SlugField/URLField/EmailField -> str (dafault로 적용되는 django 단의 유효성 검사 등의 차이)
```

* 같은 모델필드라도 DB에 따라 다른 타입이 될 수 있음




