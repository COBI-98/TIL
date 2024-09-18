#  **Nginx**
## 1. **Nginx란?**
Nginx는 `고성능 웹 서버 SW`로, <br> 
HTTP서버, 리버스 프록시 서버, 그리고 로드 밸런서로서 다양하고 중요한 기능을 수행할 수 있다. <br> 주로 경량화된 아키텍처로 인해 높은 동시 접속 처리 웹 트래픽을 효율적으로 관리하고 성능을 최적화하는 데 사용된다.

<br>

## 2. **주요 기능**

### 조회 로그 수집 및 처리
Nginx는 클라이언트의 요청에 대한 로그를 수집하여 분석할 수 있도록 지원한다. <br> 이를 통해 웹 서버의 상태 모니터링, 트래픽 분석, 보안 감사 등을 수행할 수 있다.

```nginx
http {
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;
}
```

- `log_format`: 로그의 형식을 정의<br>
    - 여기서 클라이언트 IP, 요청 시간, 요청 메소드, 상태 코드 등을 지정
- `access_log`: 정의한 형식에 따라 로그가 저장될 파일 경로를 지정

<br>

### 정적 파일 처리
Nginx는 정적 파일(HTML, CSS, JavaScript, 이미지 등)을 빠르게 제공하는 데 최적화되어 있다. <br>
이를 통해 웹 애플리케이션의 정적 리소스를 효율적으로 처리하게된다.

```nginx
http {
    server {
        listen 80;
        server_name example.com;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
            try_files $uri $uri/ =404;
        }
    }
}
```
- `root`: 정적 파일이 위치한 디렉토리를 지정
- `index`: 기본 페이지로 사용할 파일을 지정
- `try_files`: 요청된 파일이 존재하지 않을 때 대체할 파일을 지정

<br>

### 리버스 프록시
리버스 프록시는 클라이언트 요청을 백엔드 서버로 전달하고, 백엔드 서버의 응답을 클라이언트에게 반환하는 역할을 수행할 수 있다.<br> 
Nginx는 리버스 프록시 설정을 통해 로드 밸런싱, 캐싱, SSL 종료 등의 기능으로 사용된다.

```nginx
http {
    upstream backend {
        server backend1.example.com;
        server backend2.example.com;
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

- `upstream`: 리버스 프록시할 백엔드 서버를 정의
- `proxy_pass`: 클라이언트 요청을 전달할 백엔드 서버를 지정
- `proxy_set_header`: 프록시 요청 시 전달할 HTTP 헤더를 설정

<br>

### SSL/TLS 처리
Nginx는 SSL/TLS 암호화를 통해 클라이언트와의 안전한 통신을 보장하여 통해 웹 사이트의 보안성을 제공할 수 있다.
```
http {
    server {
        listen 443 ssl;
        server_name example.com;

        ssl_certificate /etc/ssl/certs/example.com.crt;
        ssl_certificate_key /etc/ssl/private/example.com.key;

        location / {
            proxy_pass http://backend;
        }
    }
}
```
- `listen 443 ssl` : SSL을 사용하여 HTTPS 프로토콜을 통해 통신을 수신하도록 설정
- `ssl_certificate`: SSL 인증서의 경로를 지정
- `ssl_certificate_key`: SSL 인증서의 개인 키 경로를 지정

<br>

## 3. Nginx conf 주요 설정 정리

### nginx.conf sample

> nginx의 기본적인 설정 파일
```nginx
user nginx;
worker_processes 1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
events {
  worker_connections 1024;
}
http {
  upstream app {
    server java-api-server:8080;
  }
  server {
    listen 80;
    server_name localhost;
    location / {
      proxy_pass http://app;
    }
  }
    
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;
  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
  access_log  /var/log/nginx/access.log  main;
  sendfile        on;
  #tcp_nopush     on;
  keepalive_timeout  65;
  #gzip  on;
  include /etc/nginx/conf.d/*.conf  //
}
```

<br>


### **각 설정별 의미**

블록 `{}`으로 지정되어 블록 안에서 설정해야 하는 것도 있고 블록 밖에 단독으로 설정 가능한 것도 있다.

### `1) 블록 밖`

#### (1) user

OS의 계정, 사용자를 의미. 보안상 root로 설정해놓지 않는 것이 좋다.

#### (2) worker_processes

이벤트를 처리할 워커 프로세스를 몇 개로 운용할 것인지 설정하는 명령어.

1이면 하나의 프로세스로 실행하겠다는 의미이며, 보통 cpu의 코어 수만큼 설정하거나 auto로 명시하는 경우가 많다.

#### (3) error_log

nginx 에러 로그가 쌓이는 경로와 로그 레벨을 설정할 수 있는 명령어.

[경로] + [로깅 레벨]로 설정한다.

- ex) error_log /var/log/nginx/error.log warn;

로깅 레벨에는 debug, info, notice, warn, error, crit와 같은 종류가 있다.

#### (4) pid

nginx의 마스터 프로세스 id 정보가 저장되는 경로를 지정

<br>

### `2) events 블록`

#### (1) worker_connections

하나의 워커 프로세스가 처리할 수 있는 커넥션(요청)의 숫자.

기본 설정은 1024로 설정되어 있다.

<br>

### `3) http 블록`

#### (1) include

conf에 포함시킬 외부 파일들의 경로를 지정하는 명령어.

중요한 것은 단순히 경로를 지정하는 것에서 끝나는 것이 아니라 구체적으로 파일까지 지정하거나 아스테릭(*)을 쓸 경우 불러올 파일 확장자까지 명시해줘야 한다.

#### (2) default_type

웹 서버의 기본 Content-Type을 지정.

#### (3) access_log

엑세스 로그가 쌓이는 경로를 지정.

#### (4) log_format

access_log에서 설정한 경로에 쌓일 로그의 형식을 지정.

#### (5) upstream 블록

upstream 서버를 지정하는 명령어. nginx가 downstream이 된다.

nginx와 연결한 웹 어플리케이션 서버를 지정하는데 사용한다.

- server 호스트주소:포트

#### (6) server 블록

하나의 서버에 대해 정의하는데 사용되는 블록.

server 블록은 여러 개일 수 있다. 서버 블록이 여러 개라는 의미는 하나의 호스트에서 여러 서버를 서빙한다는 의미이다.

##### listen

해당 서버가 바라보는 포트를 지정한다.

##### server_name

서버의 이름을 지정한다. 클라이언트가 접속하는 서버(도메인)으로 작성하면 된다.

서버 이름을 통해서 요청(request)의 헤더 값에 있는 내용과 일치하는 서버 이름이 있는지 확인 한 후에 분기 처리한다.

##### error_page

에러 처리를 위해 사용하며, 응답 http 상태 코드에 따라 지정된 url로 이동하게 만든다.

보여줄 html 페이지와 location에 해당 html의 경로를 지정해줘야 에러 응답시 성공적으로 정의된 에러 페이지로 전환된다.

#### (7) location 블록

server 블록 내에서 사용되는 블록으로, 어떤 요청이 들어왔을 때 그 요청에 맞는 곳으로 요청을 전달해주는 블록이다.

##### location / {}

슬래시(/)만 하나 있는 location 블록의 경우 보여줄 페이지들이 속해 있는 경로와, 초기 페이지를 설정할 수 있다.

```nginx
server {
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;
    }
}
```

##### proxy_pass

location 블록에 특정 경로가 지정되어 있거나 정규식으로 표현이 되어 있을 경우, 클라이언트가 해당 설정과 일치하는 url로 요청을 했을 때 proxy_pass로 어느 서버로 해당 요청을 넘겨줄 것인지 정의할 수 있다.

#### (8) ssl_certificate / ssl_certificate_key

ssl 인증서 설정 경로를 지정한다.

#### (9) keepalive_timeout

클라이언트와 연결을 유지할 시간을 설정한다. 기본값은 65이다.


<br>

### **패턴 매칭 우선 순위**

#### 1순위: `=`

정규식이 정확하게 일치하는 경우(exactly)

`location = [패턴] {...}`형태로 사용

```nginx
# /admin, /admin?param은 가능하지만, /admin/, /admins 등은 매칭 불가능함
location = /admin {
    
}
```

#### 2순위: `^~`

우선순위를 부여하고, 정규식 앞부분이 일치하는 경우

`location ^ ~ [패턴] {...}` 형태로 사용

※ `^ ~`와 다른 개념임

```nginx
location ^~/admin {
    
}
```

#### 3순위: `~`

정규식 대소문자가 일치하는 경우

`location ~ [패턴] {...}`형태로 사용

```nginx
# /admin, /admin?param에 대해서는 매칭되지만, /ADMIN, /admin/, /admind 등에 대해서는 매칭되지 않음
location ~^/admin$ {
    
}
```

#### 4순위: `~*`

대소문자를 구분하지 않고 일치

`location ~*[패턴]`형태로 사용

```nginx
location ~*.(jpg|jpeg|png|gif) {
    
}
# /admin, /ADMIN, /admin?param 등에 대해서는 일치하지만, /admin/, /ADMIN/, /admind 등에 대해서는 매칭되지 않음
location ~* ^/admin$ {
    
}
```

#### 5순위: `/`

`location /[패턴] {...}`형태로 사용

```nginx
# admin, admin?param, admin/, admind 등에 대해서 매칭
location /admin {
    
}
```

<br>

### ^

> 시작
`^[문자열]`형태로 사용하여 특정 문자열로 시작하는 패턴으로 사용

```
예시: ^a
- 패턴과 일치하는 경우: apple, air
- 패턴과 불일치하는 경우: siwon, park
```

<br>

### $

> 끝
`$[문자열]`형태로 사용하여 특정 문자로 끝나는 패턴으로 사용

```
예시: n$
- 패턴과 일치하는 경우: sun, siwon
- 패턴과 불일치하는 경우: apple, air
```

<br>

### .

> 모든 문자
`.`단독으로 쓰여 모든 문자를 의미함

```
예시: nginx.
- 패턴과 일치하는 경우: nginx, nginxw, nginx1
- 패턴과 불일치하는 경우: ngin, ngix
```

<br>

### []

> 집합
`[특정 문자 - 문자]`나 `[숫자-숫자]`형태로 사용하여 해당 집합 내의 패턴과 일치 유무를 판별하기 위해 사용

```
예시: nginx[a-c123-]
- 패턴과 일치하는 경우: nginxa, nginx1, nginx2
- 패턴과 불일치하는 경우: nginxz, nginx5 nginxo
```

<br>

### [^ ]

> 부정집합
집합의 반대 개념. 특정 메타문자에 포함되지 않는 패턴으로 사용

<br>

### |

> 또는(OR)
메타문자별로 OR

<br>

### ()

> 그룹
여러 패턴 요소를 한 단위로 묶음. 주로 `|` 과 함께 사용됨

```nginx
(jpg|jpeg|png|gif)
```

<br>

### \

> 이스케이프
`\`뒤 문자열에 대해 특수문자를 인식할 수 있도록 처리함(=문자열 그 자체로 처리)

<br>

### *

> 없거나 1회 이상의 문자열이 `*` 자리에 존재
```nginx
# 예시
ng*inx # nginx, ngxxx..inx와 매칭
.* # 모든 문자가 최소 0개 이상
```

<br>

### +

> `+`자리에 최소 1개 이상의 문자열이 존재
```nginx
# 예시
ng+inx # ngxxx..inx와 매칭 # nginx와는 불일치
```

<br>

### ?

> `?`자리에 없거나 1개의 문자열이 존재
```nginx
# 예시
ng?inx
```

### {n}

> n회
`{n}`자리에 오는 패턴 요소가 n회 있어야 함

### {n,}

> n회 이상
`{n,}`자리에 오는 패턴 요소가 n회 이상 있어야 함

### {n1, n2}

> n1회에서 n2회까지
`{n1, n2}`자리에 오는 패턴 요소가 n1회 이상, n2회 이하 있어야 함

<br>

## 4. 지시어(directive)

### return

> 문자나 URL을 지정하여 HTTP 상태 코드에 따라 요청한 처리를 반환하기 위해 사용
```nginx
# 예시
location /swagger-ui {
    return 301 http://localhost:8080/swagger-ui.html;
}
```

<br>

### redirect

>  정규 표현식 및 패턴 매칭으로 요청받은 URL을 redirect로 지정한 주소로 바꿔서 요청함
특정 uri로 요청하더라도 해당 uri를 유지하지 않고 redirect로 맵핑되어있는 주소로 요청함

<br>

### rewrite

> 정규 표현식 및 패턴 매칭으로 요청받은 URL을 조작하고 경로를 재지정함
redirect와는 달리 내부적으로 요청을 처리하고 uri는 기존에 호출했던 uri를 유지함

`rewrite [정규식] [rewrite할 내용]` 과 같이 사용

```nginx
location /api {
    rewrite ^/api(.*)$ $1?$args break;
    error_log /var/log/nginx/rewrite_debug.log debug;
    proxy_pass   http://localhost:8080;
    ...
}
```

`$args`를 사용하면 query string으로 보낸 argument에 대해서도 접근이 가능하다.

`[패턴]$`부분이 `$1`에 해당하는 부분이 된다. <br> 이 때, `$`은 정규식 매칭에서 특정 문자열로 끝나는 의미가 아니라 capturing group의 의미로 사용된다.

즉, 위 예시에서 `/api/post?id=1`이라는 요청이 들어왔을 때, 내부적으로 `/post?id=1`로 rewrite되어 8080서버에 전달된다. 

<br>

## 정리 
각 기능은 서버의 요구 사항에 맞게 커스터마이즈될 수 있으며, 이러한 설정을 통해 Nginx의 다양한 기능을 활용할 수 있다.
또한 Nginx 설정은 보안, 성능, 확장성을 고려하여 세심하게 관리해야 할 것 같다.