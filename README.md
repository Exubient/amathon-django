# EC2 + S3 + RDS를 사용해 Django 웹 서버 구축하기
###### Amathon 사전 교육 세션

## AWS Free Tier 가입하기
![스크린샷, 2018-01-10 20-23-00](https://i.imgur.com/MTm3XV4.png)

**Ctrl 누르고 왼쪽클릭!**
**https://aws.amazon.com/free/**

* 무료 계정 생성
    - 이때, 해외결제가 되는 신용카드 혹은 체크카드가 있어야 합니다.
    - 회원가입을 하면 카드 확인용으로 $1가 빠져나갑니다. (나중에 돈은 다시 돌려줍니다.)
    - 모든 가입 정보들은 **영문** 으로 작성되어야 합니다.
## C9
![c9](https://i.imgur.com/rzZMKYN.png)

AWS Cloud9은 인터넷만 연결되어 있다면 웹 브라우저상으로 코드 작성 및 실행, 디버깅을 할 수 있는 클라우드 기반의 통합 개발 환경(IDE)를 의미합니다.
**Ctrl + 왼쪽마우스 클릭!**
**https://aws.amazon.com/ko/cloud9/**
<br>

* 싱가폴 리전 선택
![스크린샷, 2018-01-10 20-35-15](https://i.imgur.com/C4v5zVW.png)

* AWS Cloud9 시작하기 버튼 --> 클릭
![스크린샷, 2018-01-10 20-38-12](https://i.imgur.com/jDNs9SR.png)
* 지역은 싱가폴로 선택을 하도록 하겠습니다.
![스크린샷, 2018-01-10 20-42-17](https://i.imgur.com/G1HBFzt.png)
* Create Environment 버튼 --> 클릭
* Create a new instance for environment (EC2 설정) --> Instance Type은 t2.micro설정
![스크린샷, 2018-01-10 20-49-26](https://i.imgur.com/5ivNdsk.png)
* Cost-saving setting은 4시간 후 설정
* Create! 하면 조금 시간이 걸립니다...
    * 이때, 자동으로 EC2가 생성됩니다.

## AWS Elastic IP (고정아이피 할당)
* [Ctrl + 마우스 왼쪽 버튼 클릭!](https://aws.amazon.com/ko/)
* 내계정 -> AWS Management Console-> EC2
* NETWORK & SECURITY탭 -> Elastic Ips -> Allocate new address -> Allocates -> 작업-> 주소연결

## AWS Inbound 열기
* [Ctrl + 마우스 왼쪽 버튼 클릭!](https://aws.amazon.com/ko/)
* 콘솔에 접근  -> EC2 -> NETWORK & SECURITY탭
* Security Groups
* Inbound -> Edit  -> Add Rules Button -> custom -> 8000, 8080 열기 -> save
![inbound](https://i.imgur.com/MLrtqy2.png)
![스크린샷, 2018-01-10 21-30-51](https://i.imgur.com/1T7SqP1.png)


## Django
#### ***Cloud 9 환경에서 개발은 굉장히 비효율적입니다. 세션의 특성상 환경을 통일하고, Django 기본 세팅 및 AWS 서비스 연동 강좌입니다***
* pip version 확인 및 freeze
    ```bash
    $ pip --version
    $ python --version
    $ pip freeze
    ```
    
* Django project 시작
    ```bash
    $ django-admin startproject amathon
    ```
    
* App 생성 및 templates폴더 생성
    ```bash
    $ django-admin startapp blog
    $ cd blog
    $ mkdir templates
    ```
    templates/main.html에 html 적용 -> hello world정도
    
* Allowed Host에 해당 IP 추가/ INSTALLED_APPS 앱 추가
    ```amathon/settings.py
    ALLOWED_HOSTS = ['*'] or ALLOWED_HOSTS['<<<<해당 public IP >>>>>>']
    
    INSTALLED_APPS = [
    ...
    'blog'
    ]
    ```
* blog/views.py
    ```
    from django.shortcuts import render, redirect

    # Create your views here.
    def main(request):
        return render(request, "main.html")
    ```

* amathon/urls.py
    ```
    from django.contrib import admin
    from django.urls import path
    from django.conf.urls import url
    from blog import views
    urlpatterns = [
        path('admin/', admin.site.urls),
        url(r'^', views.main)
        ]
    ```

* blog/settings.py
해당 코드는 RDS 생성후 production DB 연결 부분입니다
    ``` amathon/settings.py
    #DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'amathon',
        'USER': 'root',
        'PASSWORD': os.environ['amathon'], #비밀번호는 환경변수로
        'HOST' : '<<<<<해당 HOST endpoing>>>>',
        'PORT': '5432', #default port입니다
        }
    }
    ```
    ``` bash
    $ python manage.py makemigrations #if there was any change in models.py file
    $ python manage.py migrate
    ```

* blog/settings.py
해당 코드는 S3 생성후 static store 연결 부분입니다
https://www.caktusgroup.com/blog/2014/11/10/Using-Amazon-S3-to-store-your-Django-sites-static-and-media-files/
    ``` amathon/settings.py
    INSTALLED_APPS= [
    ...
    'storages'
    ]
    STATIC_URL = '/static/'
    AWS_ACCESS_KEY_ID = os.environ['AK'] #환경변수로 사용
    AWS_SECRET_ACCESS_KEY = os.environ['SK'] #환경변수로 사용
    AWS_STORAGE_BUCKET_NAME = '<<<<bucket name>>>>'
    AWS_S3_CUSTOM_DOMAIN = '%s.s3.amazonaws.com' % AWS_STORAGE_BUCKET_NAME
    AWS_S3_OBJECT_PARAMETERS = {
        'CacheControl': 'max-age=86400',
    }
    AWS_LOCATION = 'static'

    STATICFILES_DIRS = [ os.path.join(BASE_DIR, 'blog/static')]
    STATIC_URL = 'https://%s/%s/' % (AWS_S3_CUSTOM_DOMAIN, AWS_LOCATION)
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATICFILES_LOCATION = 'static'
    ```
    
    ``` bash
    #cloud9에서는 권한 문제가 있어서 실습 불가능/ Mac, Linux환경에서 가능(sudo 권한) 
    $ pip install boto3 django-storages
    #collecstatic 사용하여 S3에 업로드
    $ python manage.py collectstatic 
    ```
