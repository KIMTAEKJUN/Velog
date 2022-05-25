## Helmet이란 ?
> **Express** 사용시 **Http 헤더 설정**을 자동으로 바꾸어 **웹 취약성**으로부터 **서버**를 **보호**해주는 **보안 모듈**입니다.

<br>

### Helmet 설치
**npm**이나 **yarn**을 통해서 **helmet** 설치한 후,
```
npm install helmet --save
or
yarn add helmet @types/helmet --save
```
<br>

### Helmet 사용
```JS
const express = require('express');
const helmet = require('helmet');
const app = express();

app.use(helmet());
```
<br>

### 🚫 주의사항 🚫
**app.use(helmet());** 만 설정 할 경우 **기본적인 보안(X-Powered-By 등)**만 **설정**해 주며

**csp, expectCt, hpkp, noCache, referrerPplicy**는 적용되지 않는다.

이를 적용하려면 위와 같이 별도로 설정한다. (각 미들웨어 마다 별도로 설정을 해야한다.)
```JS
app.use(helmet.noCache());
app.use(helmet.frameguard());
```
<br>

**default**로 실행되는 **미들웨어**를 사용하지 않도록 **설정**할 수 있다.
```JS
app.use(
  helmet({
    frameguard: false,
  })
);
```
<br>

### Helmet을 사용하지 않을 시 최소한의 보안
**Express**는 별도의 설정이 없을 경우 **X-Powered-By** 헤더를 사용하도록 기본 설정이 있음.

#### **X-Powered-By 헤더 제거하기**
```JS
const express = require('express');
const helmet = require('helmet');
const app = express();

app.disable('x-powered-by');
```
<br>

## Helmet에 포함된 9가지의 미들웨어
> ### csp
**csp**는 **Content-Security-Policy**이다. 브라우저에서 사용하는 컨텐츠 기반의 보안 정책으로 **XSS**나 **Data Injection, Click Jacking** 등 웹 페이지에 악성 스크립트를 삽입하는 공격기법들을 막기 위해 사용된다.

<br>

> ### hidePoweredBy
헤더에서 **X-Powered-By**를 제거한다. 이는 서버에 대한 정보를 제공해주는 역할로 나 같은 경우는 이 영역에 **Express**라고 표기됨을 확인할 수 있었다. 이 정보는 악의적으로 활용될 가능성이 높기에 헬멧을 통해서 제거해 주는 것이 좋다.

<br>

> ### HPKP
**Public Key Pinning** 헤더를 추가하여, 위조된 인증서를 이용한 **중간자 공격** 방지합니다.

<br>

> ### HSTS
**HTTP Strict Transport Security**의 약자로 웹 사이트에 접속할 때 강제적으로 **HTTPS**로 접속하게 강제하는 기능이다. 
사용자가 특정 사이트에 접속할 때 해당 사이트가 **HTTPS**를 지원하는지, 하지 않는지를 미리 모르는 경우가 대부분이다. 그렇기에 브라우저는 디폴트로 **HTTP**로 먼저 접속을 시도한다. 이때 **HTTPS**로 지원되는 사이트였다면 **301Redirect**나 **302 Redirect**를 응답하여 **HTTPS**로 다시 접속하도록 한다. <br>
하지만 이때 **해커**가 **중간자 공격**을 하여, 중간에 **프록시 서버**를 두고
[나] <-> [해커] 사이에서는 **HTTP** 통신을 하고 [해커] <-> [웹사이트] 사이에선 **HTTPS** 통신을 한다면,
우리의 개인정보가 **HTTP 프로토콜**을 통해 해커에게로 전해지는 참사가 일어난다.
이러한 공격을 **SSL Stripping**이라고 하며 이런 공격을 방지하기 위해 **HSTS**를 설정한다.

<br>

> ### IeNoOpen
**IE8 이상**에 대해 **X-Download-Options**를 설정한다. 이 옵션은 8 버전 이상의 인터넷 익스플로러에서 다운로드된 것들을 바로 여는 것 대신 저장부터 하는 옵션이다. 사용자는 저장부터 하고 다른 응용프로그램에서 열어야 한다.

<br>

> ### noCache
**Cache-Control** 및 **Pragma** 헤더를 설정하여 **클라이언트** 측에서 **캐싱**을 사용하지 않도록 하는 설정이다. 

<br>

> ### noSniff
**X-Content-Type-Options** 를 설정하여 선언된 콘텐츠 유형으로부터 벗어난 응답에 대한 브라우저의 **MIME 스니핑**을 방지한다. **MIME**이란 **Multipurpose Internet Mail Extensions**의 약자로 클라이언트에게 전송된 문서의 다양성을 알려주기 위한 포맷이다. 브라우저는 리소스를 내려받을 때 **MIME** 타입을 보고 동작하기에 정확한 설정이 중요하다. <br>
**MIME 스니핑**이란 브라우저가 특정 파일을 읽을 때 파일의 실제 내용과 **Content-Type**에 설정된 내용이 다르면 파일로 부터 형식을 추측하여 실행하는 것인데, 편리함을 위한 기능이지만 공격자에게 악용 될 가능성이 있다.

<br>

> ### frameguard
**X-Frame-Options** 헤더를 설정하여 클릭재킹에 대한 보호를 제공한다.
클릭재킹이란 사용자가 자신이 클릭하고 있다고 인지하는 것과 다른 것을 클릭하도록 하여 속이는 해킹 기법이다. 속이기 위해 보이지 않는 레이어에 보이지 않는 버튼을 만드는 방법이 있다.

<br>

> ### xssFilter
**X-XSS-Protection**을 설정하여 대부분의 최신 웹 브라우저에서 **XSS(Cross-site scripting)** 필터를 사용하도록 합니다.

<br>

## 처음 듣는 개념 세부 설명
**1-1. SSL (Secure Socket Layer) 보안 소켓 계층**

 - **웹사이트**와 **브라우저** **( 혹은 두 서버 )** 사이에 전송된 데이터를  암호화하여 인터넷 연결을 유지하는 표준 기술, 이는 해커가 정보 및 금융 정보를 포함한 전송되는 모든 정보를 열람하거나 훔치는 것을 방지한다.
<br>
 
**1-2. TLS: 전송 계층 보안(Transport Layer Security, TLS) **

- **TLS**는 가장 최신 기술로 더 강력한 버전의 **SSL** 그러나 **SSL**이 더 일반적으로 사용되는 용어이기에, 여전히 보안 인증서는 **SSL**이라 불린다. 
<br>

**1-3. XSS (Cross Site Scripting) 공격**

 - 사이트에 **스크립트**를 넣는 기법. 공격에 성공하면 사이트에 접속한 사용자는 그 삽입된 코드를 실행하게 되며 의도치 않은 행동을 하게 된다. ( 이때 예를 들어 쿠키나 세션 토큰 등의 민감한 정보를 탈취 )
<br>
 
**1-4. Strict-Transport-Security**

 - 웹 사이트를 접속할 때 강제적으로 **https프로토콜**로만 접속하게 하는 기능.
<br>
 
## 글을 끝마치며 . . .
갑자기 NodeJs에 대한 글을 포스팅하게 되었는데, 제가 지금까지 하던
고민을 회사 과장님께 털어놓으니까 과제를 주시더라고요.
클린 코드 읽은 후, 감상문 그다음 바로 게시판 만들기 (시큐어코딩)
30일이나 31일부터 시작할 거 같은데 그때 찾아보면 좀 늦을 거 같아서 
미리 찾아놓아서 정리해놨습니다 ㅎㅎ
다음 포스팅은 클린 코드 책을 읽고 내용 정리 후 포스팅할 거 같습니다.
지금까지 부족한 제 글 읽어주셔서 감사합니다!
<br>

[참고한 블로그1](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=wlsdml1103&logNo=221460233318)
[참고한 블로그2](https://llshl.tistory.com/39)
[참고한 블로그3](https://dlwlrma0203.tistory.com/65)
[참고한 블로그4](https://7stocks.tistory.com/94)