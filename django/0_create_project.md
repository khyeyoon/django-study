# conda에서 django 시작하기

* 아나콘다로 가상환경 구축하기

'''python
conda create --name (가상환경 명) python=(원하는 파이썬 버전)
'''

* 가상환경 들어가기

'''
conda activate (가상환경 명)
'''

# django project 생성하기

* project 생성 명령어

'''
django-admin startproject (프로젝트명)
'''

'''
python -m django startproject (프로젝트명)
'''

> 위의 명령어를 통해 프로젝트를 생성할 수 있고, 프로젝트가 생성되면 manage.py 파일과 __init__.py, settings.py, urls.py, wsgi.py 등이 자동으로 생성됨

* project server 실행 명령어

'''
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
'''

> 위의 명령어를 실행시키면 서버(http://localhost:8000/admin/)에 접속할 수 있음




