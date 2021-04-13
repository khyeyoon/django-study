# conda에서 django 시작하기 (django 개발을 위한 가상환경 구축)

* 아나콘다로 가상환경 구축하기

```python

conda create --name (가상환경 명) python=(원하는 파이썬 버전)

```

* 가상환경 들어가기

```
# conda에 설치된 가상환경 확인
# conda env list

conda activate (가상환경 명)
```

* 가상환경에 django 설치하기 

```
pip install "django~=(version)"
```

# django project 생성하기

* project 생성 명령어

```
django-admin startproject (프로젝트명)
```

```
python -m django startproject (프로젝트명)
```

> 위의 명령어를 통해 프로젝트를 생성할 수 있고, 프로젝트가 생성되면 manage.py 파일과 __init__.py, settings.py, urls.py, wsgi.py 등이 자동으로 생성됨

* project server 실행 명령어

```
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

> 위의 명령어를 실행시키면 서버(http://localhost:8000/admin/)에 접속할 수 있음

## django application 생성하기

* 재사용성을 목적으로 한 파이썬 패키지

1. application 생성

```
python manage.py starapp (application 명)
```

2. (application 명)/urls.py 파일 생성

3. 프로젝트/urls.py에 include 적용

```
# 프로젝트/urls.py

from django.urls import include, path
from django.urls import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path('(application 명)/', include('(application 명).urls')),
]
```

4. 프로젝트/settings.py의 INSTALLED_APPS에 application 명 등록

```
# 프로젝트/settings.py

INSTALLED_APPS = [
    # django apps
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # third apps
    'django_extensions',
    'debug_toolbar',
    # locals apps
    'accounts',
    'blog1',
    'instagram',
    'application 명',
]
```







