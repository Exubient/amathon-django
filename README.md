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
```bash
$ git clone https://github.com/Exubient/AUSG_KakaoBot
```

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
