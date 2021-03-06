# django model 생성하기

* DB table과 python class를 1:1 mapping

```
# django model을 통해, 데이터베이스 형상을 관리
1. model class 작성
2. model class로부터 마이그레이션 파일 생성 (makemigrations)
3. 마이그레이션 파일을 데이터베이스에 적용 (migrate)
4. model 활용
```

```
# django 외부에서, 데이터베이스 형상을 관리
1. 데이터베이스로부터 model class 소스 생성 (inspectdb)
2. model 활용
```


1. (application명)/models.py에 class 정의하기
 


2. migration 파일 생성하기

```
python manage.py makemigrations (application명)
```


3. migration 파일 적용시키기

```
python manage.py migrate (application명)
```

- migration 적용내역 확인하기 

```
# 전체 application 적용내역을 확인하려면 applicataion 명을 지우고 명령어 실행

python manage.py showmigrations (application명)
```


```
# 데이터베이스에 들어가는 query 확인

python manage.py sqlmigrate (application 명) (migrations에 생성된 파일명)
```


4. database 변경사항 확인하기

```
python manage.py dbshell

# table 목록
.tables

# table schema 확인
.schema (table 명)

# 나가기
.quit
```


5. 정의한 class를 admin에 등록하기

### django admin

django.contrib.admin 앱을 통해 제공

> default 경로 : /admin/

모델 클래스 등록을 통해, 조회/추가/수정/삭제 웹 UI를 제공

> 서비스 초기에 관리도구로서 사용하기에 좋음
> 관리도구 만들 시간을 줄이고, End-User 서비스에 집중

내부적으로 Django Form을 적극적으로 사용

```
# 첫번째 방법 : 기본 ModelAdmin으로 동작
from django.contrib import admin
from .models import Item

admin.site.register(Item)
```

```
# 두번째 방법 : 지정한 ModelAdmin으로 동작
from django.contrib import admin
from .models import Item

class ItemAdmin(admin.ModelAdmin):
    pass

admin.site.register(Item, ItemAdmin)
```

```
# 세번째 방법 : wrapping 
from django.contrib import admin
from .models import Item

@admin.register(Item)
class ItemAdmin(admin.ModelAdmin):
    pass
```

* model class에 __str__ 구현하기 : admin model list에서 "모델명 object"를 원하는 대로 변경

```
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=100)
    desc = models.TextField(blank=True)
    price = models.PositiveIntegerField()
    is_publish = models.BooleanField(default=False)

    # JAVA의 toString과 유사, 객체를 출력할 때 str의 return 값 활용
    def __str__(self):
        return f"Custom Post object ({self.id})
```

* class에 다양한 속성 정의 : list_display, list_display_links, search_fields, list_filter

```
from django.contrib import admin
from .models import Item

@admin.register(Item)
class ItemAdmin(admin.ModelAdmin):
    # model list에 출력할 칼럼 지정
    list_display = ['name','short_desc','price','is_publish']
    # 지정된 칼럼 중에서 링크를 걸 속성 지정
    list_display_links = ['name']
    # 지정 필드값으로 필터링 
    list_filter = ['is_publish']
    # admin 내 검색 UI를 통해, DB를 통한 where 쿼리 대상 필드 리스트
    search_fields = ['name']

    def short_desc(self, item):
        return item.desc[:20]
```

## django로 media 파일 다루기 : 사진 업로드

* Media 파일 처리 순서

```
1. HttpRequest.FILES를 통해 파일 전달
2. 뷰 로직이나 폼 로직을 통해, 유효성 검증을 수행
3. FileField/ImageField 필드에 "경로(문자열)"를 저장
4. setting.MEDIA_ROOT 경로에 파일 저장
```

1. settings.py 파일을 통해 경로를 설정

```
# url을 통해 파일에 접근할 때 사용
MEDIA_URL = '/media/'
# 업로드된 파일이 저장되는 경로 (BASE_DIR:project root)
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

2. model class에 ImageField 추가

```
# field option : blank, upload_to
photo = models.ImageField(blank=True, upload_to='instagram/post/%Y/%m/%d')
```

> blank : 업로드 옵션처리 여부 (dafault : False)
>
> upload_to : 파일 저장시 저장경로를 계산하여 settings.MEDIA-ROOT 하위에서 저장한 파일명/경로명 결정 
>
> (default : 파일명 그대로 settings.MEDIA_ROOT에 저장)

3. 개발환경에서의 media 파일 서빙

```
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## django project 설정이 되어있는 python shell 실행하기

```
python manage.py shell
```

* anaconda로 jupyter notebook 실행하기

```
# ipython과 jupyter가 설치됨
conda install jupyter

# jupyter noetebook 창 띄우기
jupyter notebook
```

> jupyter 환경에서 django를 연동하려면 아래 코드가 추가되어 있어야 함

```
import os
import django

os.environ['DJANGO_SETTINGS_MODULE']= 'askcompany.settings'
os.environ['DJANGO_ALLOW_ASYNC_UNSAFE']='true'

django.setup()
```









 











