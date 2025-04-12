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

이처럼 하나의 페이지만으로 수많은 페이지를 표현할 수 있는 유용한 기능을 제공한다는 장점이 있지만, 사용이 자제되고 있다.

### 1) 클릭 재킹(Click Jacking)

iframe의 보안적 취약점으로 여겨지는 클릭 재킹은 해킹 공격 중 하나로서, 단어 그대로 클릭을 가로챈다는 의미를 가지고 있다.



