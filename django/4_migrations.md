# Migrations

* 모델의 변경내역을 데이터베이스 스키마로 반영시키는 효율적인 방법을 제공

* 데이터베이스에 어떤 변화를 가하는 operation들을 나열한 파일

1. migration 파일 생성 (application 명을 생략하면 전체 application에 대하여 생성)

```
python manage.py makemigrations (application 명)
```

> migration 파일이 너무 많아질 경우, squashmigrations 명령을 통해 통합 시킬 수 있음

2. 지정 migration의 SQL 내역 출력

```
python manage.py sqlmigrate (application 명) (migration 명)
```

3. 지정 데이터베이스에 migration 적용 (application 명을 생략하면 전체 application에 대하여 적용)

```
python manage.py migrate (application 명)
```

4. migration 적용 현황 출력

```
python manage.py showmigrations (application 명)
```

## migration 정방향/역방향 

```
python manage.py migrate (application 명) (migration 명)
```

> migration 명이 현재 적용된 migration 보다 이 후 라면, 정방향으로 지정 migration까지 forward 수행

> migration 명이 현재 적용된 migration 보다 이 전이라면, 역방향으로 지정 migration까지 backward 수행 (롤백)

## 필수필드에 대한 makemigrations

* 필수필드 : blank와 null 옵션이 모두 False인 필드

* makemigrations 명령을 수행할 때, 기존 record들을 어떤 값으로 채울 것인지 입력해야 함




