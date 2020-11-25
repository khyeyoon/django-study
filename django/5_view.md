# django view

view : urls.py/urlpatterns 리스트에 mapping된 호출 가능한 객체

> 1개의 HTTP 요청에 대해 1개의 view가 호출됨

1. Function Based View : django view의 기본

호출 가능한 객체

2. Class Based View 

(class 명).as_view()를 통해 호출 가능한 객체를 생성/리턴

* view 호출

1 번째 인자 : HttpRequest 객체로 현재 요청에 대한 모든 내역을 담고 있음

2-n 번째 인자 : 현재 요철의 URL로부터 capture된 문자열들

> 1. url/re_path를 통해 처리하면 모든 인자는 str 타입으로 전달

> 2. path를 통해 처리하면 mapping된 Converter의 to_python에 맞게 변환된 값으로 전달

* view의 return 값

장고 Moddleware는 view에서 HttpResponse 객체를 리턴하기를 기대하기 때문에 HttpResponse 객체를 꼭 리턴해주어야 함

> 다른 type을 리턴할 경우 Middleware에서 처리 오류 발생

HttpResponse는 파일 인터페이스 기능을 지원

```
def class(request, pk):
    response = HttpResponse()
    response.write("Hello World")
    return response
```

> 파일과 같은 객체나 str/bytes 타입의 응답도 지원

## view 활용 예시 코드

* Pandas를 통한 CSV 응답 생성

Pandas : 파이썬을 사용하여 데이터 전처리/분석을 할 때 활용하는 라이브러리

```
import pandas ad pd
from io import StringIO
from django.http import HttpResponse

def response_CSV(request):
    df = pd.DataFrame([
        [100, 110, 120],
        [200, 210, 220],
        [300, 310, 320],
    ])

    io = StringIO()
    df.to_csv(io)
    # 메모리 기반 파일 인터페이스 io
    io.seek(0)

    response = HttpResponse(io, content_type='text/csv')
    response['Content-Disposition'] = "attachment; filename*=utf-8''{}".format(encoded_filename)
    return response
```

* Pandas를 통한 엑셀 응답 생성

```
import pandas as pd
from io import BytesIO
from urllib.parse import quote
from django.http import HttpResponse

def response_excel(request):
    df = pd.DataFrame([
        [100, 110, 120],
        [200, 210, 220],
    ])

    io = BytesIO()
    df.to_excel(io)
    io.seek(0)

    encoded_filename = quote('pandas.xlsx')
    response = HttpResponse(io, content_type='application/vnd.ms-excel')
    response['Content-Disposition'] = "attachment; filename*=utf-8'' {}".format(encoded_filename)
    return response
```

* Pillow를 통한 이미지 응답 생성

해당 Http를 호출할 때 마다 동적으로 이미지를 새롭게 생성

```
import requests
from io import BytesIO
from PIL import Image, ImageDraw, ImageFont

def response_pillow_image(request):
    # font 경로
    ttf_path = 'C:/Windows/Fonts/malgun.ttf'

    # 이미지 파일 다운로드 혹은 로컬 디스크 상의 이미지 직접 열기
    image_url = 'http://www.flowermeaning.com/flower-pics/Calla-Lily-Meaning.jpg'
    # 서버로 HTTP GET 요청하여 응답 획득
    res = requests.get(image_url)
    # 응답의 Raw Body 메모리 파일 객체 BytesIO instance 생성
    io = BytesIO(res.content)
    # 파일의 처음으로 커서를 이동
    io.seek(0)

    canvas = Image.open(io).convert('RGBA')
    font = ImageFont.truetype(ttf_path, 40)
    draw = ImageDraw(canvas)

    text = 'Ask Company'
    left, top = 10, 10
    margin = 10
    width, height = font.getsize(text)
    right = left + width + margin
    bottom = top + height + margin
    draw.rectangle((left, top, right, bottom),(255,255,224))
    draw.text((15,15), text, font=font, fill=(20, 20, 20))

    response = HttpResponse(content_type='image/png')
    # HttpResponse의 file-like 특성을 활용
    canvas.save(response, format='PNG')
    return response
```





