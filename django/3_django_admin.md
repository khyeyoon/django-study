## django admin

django.contrib.admin 앱을 통해 제공

> default 경로 : /admin/

모델 클래스 등록을 통해, 조회/추가/수정/삭제 웹 UI를 제공

> 서비스 초기에 관리도구로서 사용하기에 좋음
> 관리도구 만들 시간을 줄이고, End-User 서비스에 집중

내부적으로 Django Form을 적극적으로 사용

### model class를 admin에 등록

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
