# django에서 데이터베이스 인터페이스를 제공

1. SELECT 

'''
from (application 명).models import cls

qs=cls.objects.all()

# 해당 query 명 출력 : database에서 어떻게 동작하는지 확인할 수 있음
print(qs.query)
'''

2. SELECT query에 WHERE 추가

'''
# 해당 조건을 포함
qs.filter(...)

# 해당 조건을 포함하지 않는 것
qs.exclude(...)

# 특정 모델객체 1개 획득 (1개가 아닐 경우 예외 발생)
qs[index] 

# 특정 모델객체 1개 획득 (해당 조건이 1개가 아닐 경우 예외 발생)
qs.get(...)

# 특정 모델객체 1개 획득 (1개가 아니면 None 반환)
qs.first() 

qs.last()
'''

3. SELECT query에 ORDER BY 추가

* model class의 Meta 속성으로 ordering 설정(list로 지정)

'''
# 만약 queryset 코드에서 직접 order_by를 지정하면 무시됨
class Meta:
    ordering = ['...']
'''

* queryset에 order_by() 지정

'''
from (application 명).models import cls

qs=cls.objects.all().order_by('...')
'''

4. SELECT query에 OFFSET/LIMIT 추가

'''
from (application 명).models import cls

# OFFSET:start, LIMIT:stop-start (음수 인덱스 슬라이싱은 지원하지 않음)
qs=cls.objects.all()[start:stop:step]
'''

