# 04_X-Frame-Options

HTTP 응답 헤더의 옵션 중 하나로, `<frame>`, `<iframe>`, `<object>`에서 화면을 렌더링 할수 있는지 여부를 나타내는데 사용된다.

- `deny`: 어떤 사이트에서도 frame 상에서 보여질 수 없음
- `sameorigin`: 동일한 사이트의 frame에서만 보여질 수 있음
- `allow-from uri`: 지정된 특정한 uri의 frame에서만 보여질 수 있음

## 1. iframe

> inline frame

frame 혹은 iframe이란 HTML 태그 중 하나로서, 웹 페이지 안에 또 다른 웹 페이지를 삽입할 수 있는 기능을 제공한다.

```html
<iframe src="삽입하는 웹페이지 url" title="제목"></iframe>
```

이처럼 하나의 페이지만으로 수많은 페이지를 표현할 수 있는 유용한 기능을 제공한다는 장점이 있지만, 단점으로 인해 사용이 자제되고 있다.

### 1) 클릭 재킹(Click Jacking)

iframe의 보안적 취약점으로 여겨지는 클릭 재킹은 해킹 공격 중 하나로서, 글자 그대로 클릭을 가로챈다는 의미를 가지고 있다.

사용자는 특정 웹 페이지를 클릭하는 것처럼 보이지만, 사실 실제 컨텐츠는 숨겨져 있고 공격에 사용되는 html 요소를 클릭하는 것이다.

웹 페이지에서 iframe 태그를 숨겨놓고 실제 페이지의 클릭을 iframe내의 클릭으로 인식되게끔 자바스크립트 코드를 작성하여 사용자 입장에서는 정상적인 동작인 것처럼 보이나 실제로는 사용자를 속여서 공격하는 것이다.

이러한 이슈 때문에 HTML 5부터는 사용 자제 혹은 사용 제외가 되었다.

<br>

## 2. "XXX"에서 연결을 거부

X-Frame-Options 옵션을 통해 iframe 호출이 막혀있다면, 다음과 같은 에러가 발생한다. (HTML 5부터는 사용이 제외되었기 때문에 기본적으로 거부되어 있는 경우가 많음)

- 도메인 ~ 에서 연결을 거부 했습니다 (net::ERR_BLOCKED_BY_RESPONSE)

설정을 해제하려면 apache, nginx와 같은 웹 서버 상의 설정이나 백엔드쪽에서 보안적인 설정을 하면 된다.

### 1) Nginx

```nginx
add_header X-Frame-Options [옵션];
```

- deny: iframe 차단
- sameorigin: 동일한 사이트 내에서만 iframe 허용
- allow-from uri: 특정 주소만 iframe 허용

혹은 해당 옵션 라인을 전체 지워줌으로써 옵션 자체를 제거할 수도 있다.
