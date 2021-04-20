## django 기본 제공 CBV 활용

```
from django.views.generic import DetailView

post_detail = DetailView.as_view(model=Post, pk_url_kwarg='id')
```

```
# 상속을 통한 CBV 속성 정의
from django.views.generic import DetailView

class PostDetailView(DetailView):
    model = Post
    pk_url_kwarg = 'id'

post_detail = PostDetailView.as_view()
```
* * *

## 장고 기본 CBV API (Base views)

Base views : View, TemplateView, RedirectView

1. View

모든 CBV의 모체이며, http method별로 지정 이름의 멤버함수를 호출하도록 구현

```
class View:
    """
    Intentionally simple parent class for all views. Only implements
    dispatch-by-method and simple sanity checking.
    """

    http_method_names = ['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']

    def __init__(self, **kwargs):
        """
        Constructor. Called in the URLconf; can contain helpful extra
        keyword arguments, and other things.
        """
        # Go through keyword arguments, and either save their values to our
        # instance, or raise an error.
        for key, value in kwargs.items():
            setattr(self, key, value)

    @classonlymethod
    def as_view(cls, **initkwargs):
        """Main entry point for a request-response process."""
        for key in initkwargs:
            if key in cls.http_method_names:
                raise TypeError(
                    'The method name %s is not accepted as a keyword argument '
                    'to %s().' % (key, cls.__name__)
                )
            if not hasattr(cls, key):
                raise TypeError("%s() received an invalid keyword %r. as_view "
                                "only accepts arguments that are already "
                                "attributes of the class." % (cls.__name__, key))

        def view(request, *args, **kwargs):
            self = cls(**initkwargs)
            self.setup(request, *args, **kwargs)
            if not hasattr(self, 'request'):
                raise AttributeError(
                    "%s instance has no 'request' attribute. Did you override "
                    "setup() and forget to call super()?" % cls.__name__
                )
            return self.dispatch(request, *args, **kwargs)
        view.view_class = cls
        view.view_initkwargs = initkwargs

        # take name and docstring from class
        update_wrapper(view, cls, updated=())

        # and possible attributes set by decorators
        # like csrf_exempt from dispatch
        update_wrapper(view, cls.dispatch, assigned=())
        return view

    def setup(self, request, *args, **kwargs):
        """Initialize attributes shared by all view methods."""
        if hasattr(self, 'get') and not hasattr(self, 'head'):
            self.head = self.get
        self.request = request
        self.args = args
        self.kwargs = kwargs

    def dispatch(self, request, *args, **kwargs):
        # Try to dispatch to the right method; if a method doesn't exist,
        # defer to the error handler. Also defer to the error handler if the
        # request method isn't on the approved list.
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
        return handler(request, *args, **kwargs)

    def http_method_not_allowed(self, request, *args, **kwargs):
        logger.warning(
            'Method Not Allowed (%s): %s', request.method, request.path,
            extra={'status_code': 405, 'request': request}
        )
        return HttpResponseNotAllowed(self._allowed_methods())

    def options(self, request, *args, **kwargs):
        """Handle responding to requests for the OPTIONS HTTP verb."""
        response = HttpResponse()
        response.headers['Allow'] = ', '.join(self._allowed_methods())
        response.headers['Content-Length'] = '0'
        return response

    def _allowed_methods(self):
        return [m.upper() for m in self.http_method_names if hasattr(self, m)]
```

2. TemplateView : TemplateResponseMixin, ContextMixin, View을 상속 받음

```
class TemplateView(TemplateResponseMixin, ContextMixin, View):
    """
    Render a template. Pass keyword arguments from the URLconf to the context.
    """
    def get(self, request, *args, **kwargs):
        context = self.get_context_data(**kwargs)
        return self.render_to_response(context)
```

3. RedirectView 

주소를 변경시켜주는 view

```
class RedirectView(View):
    """Provide a redirect on any GET request."""
    # True : 301 응답, 영구적인 이동 / False : 302 응답, 임시 이동
    permanent = False
    # URL 문자열
    url = None
    # URL Reverse를 수행할 문자열
    pattern_name = None
    # QueryString을 그대로 넘길 것인지 여부
    query_string = False

    def get_redirect_url(self, *args, **kwargs):
        """
        Return the URL redirect to. Keyword arguments from the URL pattern
        match generating the redirect request are provided as kwargs to this
        method.
        """
        if self.url:
            url = self.url % kwargs
        elif self.pattern_name:
            url = reverse(self.pattern_name, args=args, kwargs=kwargs)
        else:
            return None

        args = self.request.META.get('QUERY_STRING', '')
        if args and self.query_string:
            url = "%s?%s" % (url, args)
        return url

    def get(self, request, *args, **kwargs):
        url = self.get_redirect_url(*args, **kwargs)
        if url:
            if self.permanent:
                return HttpResponsePermanentRedirect(url)
            else:
                return HttpResponseRedirect(url)
        else:
            logger.warning(
                'Gone: %s', request.path,
                extra={'status_code': 410, 'request': request}
            )
            return HttpResponseGone()

    def head(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)

    def options(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)

    def patch(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)
```

* * *

## 장고 기본 CBV API (Generic display views)

1. DetailView

1개 model의 1개 object에 대한 template 처리

> model 명 소문자 이름의 Model Instance를 template에 전달

부모 클래스 : SingleObjectTemplateResponseMixin, BaseDetailView

> SingleObjectTemplateResponseMixin : template_name이 지정되지 않았다면, 모델명으로 template 경로 유추

> BaseDetailView : 부모 클래스 SingleObjectMixin에서 url_kwarg로 지정된 Model Instance 획득

```
from django.views.generic import DetailView
from .models import Post

post_detail = DetailView.as_view(model=Post)
```

```
from django.views.generic import DetailView
from .models import Post

class PostDetailVide(DetailView):
    model = Post

post_detail = PostDetailView.as_view()
```

2. ListView

1개 model에 대한 List template 처리

페이징 처리 지원 (한 페이지에 몇개의 Post를 볼건지 정의할 수 있음)

> model 명 소문자_list 이름의 QuerySet을 template에 전달

부모 클래스 : MultipleObjectTemplateResponseMixin, BaseListView

> MultipleObjectTemplateResponseMixin : template_name이 지정되지 않았다면, 모델명으로 template 경로 유추

> BaseListView : 부모 클래스 MultipleObjectMixin에서 Paginator가 적용된 QuerySet 획득

```
from django.views.generic import ListView
from .models import Post

post_list1 = ListView.as_view(model=Post)

# 페이징 처리
post_list2 = ListView.as_view(model=Post, paginate_by=10)

# 상속을 통해 함수 재정의
class PostListView(ListView):
    model = Post
    paginate_by = 10

    def get_queryset(self):
        qs = super().get_queryset()
        qs = qs.filter(...)
        return qs

post_list3 = PostListView.as_view()
```

## 장고 기본 CBV API (Generic Date Views)

- ArchiveIndexView : 지정 날짜필드 역순으로 정렬된 목록 (최신 목록을 보기 위해 사용)

- YearArchiveView : 지정 year년도의 목록

- MonthArchiveView : 지정 year/month 월의 목록

- WeekArchivesView : 지정 year/week 주의 목록

- DayArchivesView : 지정 year/month/day 일의 목록

- TodayArchivesView : 오늘 날짜의 목록

- DateDetailView : 지정 year/month/day 목록 중에서 특정 pk의 detail











