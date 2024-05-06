# 00_React 동작 원리

> React는 어떻게 동작할까?

## 1. React(리액트)

리액트는 라이브러리일까? 프레임워크일까?

- 둘 다 해당한다고 보는 게 맞다.
- 라이브러리라는 관점; JS 라이브러리로서 UI 컴포넌트를 구성하고 관리하는데 사용되는 라이브러리다.
- 프레임워크라는 관점; 상태 관리, 라우팅 등을 제공하며 어플리케이션 전체 구조를 정의하고 제어하는 역할을 하기 때문에 프레임워크다.

<br>

## 2. 웹 페이지 렌더링 과정

> HTML + CSS + JS가 웹 페이지를 렌더링 하는 과정

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/185ec1b5-60e9-494e-b8f1-1f1944ae34fd)

```
1. HTML parser가 HTML을 바탕으로 DOM tree를 그린다.
2. CSS parser가 CSS를 바탕으로 CSSOM을 그린다.
3. DOM에 CSSOM을 적용하여 Render Tree를 그린다.
4. Render Tree를 바탕으로 Painting 하여 실제 화면에 렌더링 한다.
*HTML 코드를 읽어 내려가다가 <script></script> 태그를 만나면 파싱을 잠시 중단하고 js 파일을 로드한다.
```

### 1) DOM

> Document Object Model

HTML 요소들의 구조화된 표현을 의미한다. 즉, HTML을 트리 형태의 객체로 표현한 것을 DOM이라고 한다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/50f8f313-d6fd-49ff-b5be-9b4d83d60433)

텍스트인 HTML을 브라우저와 Javascript가 이해할 수 있는 구조로 HTML Parser가 DOM으로 변환시켜 주는 것이다.

<br>

### 2) CSSDOM

> DOM + CSS

CSSDOM이란 DOM에 CSS가 적용된 모델을 의미한다.

<br>

### 3) DOM API

> DOM을 제어할 수 있는 API

웹은 크게 정적 웹과 동적 웹으로 나뉘는데, 이 때 웹을 동적으로 제어하는 역할을 담당하는 것이 Javascript이다.

Javascript가 동적으로 웹을 조작할 수 있는 이유는 바로 DOM API를 활용하기 때문에 가능한 것이다.

DOM API를 통해 DOM에 요소(element)를 추가/수정/삭제하는 방식으로 제어하는 것이다.

#### (1) vanilla javascript와 jQuery

jQuery라는 라이브러리는 vanilla javascript와 함께 오랫동안 동적 웹 생태계를 지배해왔다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/9b13ca0c-364b-4255-abbb-5b328deaee7b)

특히, jQuery는 "write less, do more"라는 모토대로 바닐라 자바스크립트보다 더 적은 양, 더 직관적인 코드로 개발이 가능했기 때문에 인기를 끌었다.

그러나 다음과 같은 단점이 존재했다.

- 바닐라 JS는 jQuery에 비해 직관적이지는 않았지만 로드해야할 패키지가 없어서 성능적으로 뛰어났던 반면, jQuery는 로드해야할 패키지 때문에 성능적으로 떨어졌다. 
- jQuery는 모듈화나 컴포넌트화가 어려워 유지보수가 어렵다.
- 현대 웹 트렌드는 Interactive Web(상호 작용하는 웹)인데, jQuery는 직접 DOM을 조작하기 때문에 연산이 자주, 많이 발생하여 브라우저의 성능이 낮아지는 문제가 발생했다.

<br>

## 3. Virtual DOM

> 가상 DOM

실제 DOM을 조작하는 것은 너무 low-level이고 성능상으로 좋지 않다.

따라서 가상 DOM이라는 개념의 Virtual DOM이라는 개념이 등장하였고, 직접적으로 DOM을 변경하지 않고 Virtual DOM에 변화가 있을 때 업데이트된 Virtual DOM과 변경 전의 Virtual DOM의 비교를 통해 변경된 부분만 Real DOM에 반영하는 형태로 최적화를 하였다.

Real DOM과 Virtual DOM의 관계는 다음과 같이 비유할 수 있다.

- Real DOM - TV 화면
- Virtual DOM을 통한 조작 - 리모컨



Virtual DOM이 바로 리액트(React)가 동작하는 핵심 과정 중 하나이며, 비교를 통한 DOM 조작 과정은 `Diffing 알고리즘`을 통해 이뤄진다.

### 1) Virtual DOM이 효율적인 이유?

- Real DOM에 대한 조작은 실제 DOM 객체를 전부 다 교체 후 다시 렌더링 해야하기 때문에 비용이 비싸다. 그러나 Virtual DOM의 경우 메모리에만 존재하기 때문에 보다 빠르게 접근해서 필요한 부분만 수정이 가능하다.
  - 가상 DOM은 실제 렌더링이 되지 않기 때문에 연산 비용이 저렴함.
- 또한 Virtual DOM은 Javascript 객체로 표현되어 있어서 Real DOM에 비해 훨씬 가볍고, 수정이 쉽다.

<br>

### 2) Diffing 알고리즘

> 재조정(Reconciliation)

리액트 API는 `선언적`이다. 무엇이 변경되었고 갱신되었는지 굳이 걱정할 필요 없이 [diffing 알고리즘](https://ko.legacy.reactjs.org/docs/reconciliation.html)에 의해서 컴포넌트의 변화를 갱신한다.

기존 거리 기반 연산 알고리즘은 n개의 엘리먼트에 대해 O(n^3)의 시간 복잡도 성능을 보여줬기 때문에 리액트는 휴리스틱 알고리즘에 기반하여 두 가지 핵심 가정을 통해 O(n)의 성능을 보이는 알고리즘을 적용하고 있다.

- 루트 엘리먼트 타입이 다른 경우, 이전 트리를 버리고 완전히 새로운 다른 트리로 재구성한다.
- key 속성을 통해서 렌더링 시 어떤 자식 엘리멘트가 변경되지 않아야 할지 구분/표시한다.

<br>

## 4. React의 동작

> Virtual DOM + Single Page Application + Webpack

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/fb3f3794-f14f-436c-ae46-58e98586978b)

### 1) SPA(Single Page Application)

리액트는 SPA(Single Page Application)이다. 이 말인 즉슨, `index.html` 하나만 가지고 웹 페이지를 렌더링한다.

그리고 동적 스크립트인 `App.js`를 하나만 로드하여 Interactive Web을 렌더링 하는 것이다.

이렇게 서버에서 딱 한 번만 파일을 받아온 이후에 API 요청 외 모든 요청은 클라이언트(프론트)에서 처리하기 때문에 사용자와 웹이 상호작용하는 속도가 빠른데, 인터랙션 과정에서 서버가 개입되지 않기 때문이다.

이를 `CSR(Client Side Rendering)`이라고 한다.

- 반대 개념으로 SSR(Server Side Rendering)이라고 한다.
- CSR은 처음 로딩 시 하나의 페이지를 가져와서 스크립트까지 최초로 호출 완료는 해야하기 때문에 첫 화면을 보기까지 오랜 시간이 걸릴 수 있다는 단점이 있다.
- 또한 index.html 하나만을 사용하기 때문에 `SEO(검색 엔진 최적화, Search Engine Optimization)`에 불리하다는 단점이 있다. (그래서 보면 리액트 프론트 개발자 들이 SEO를 위해 노력을 기울이는 것도 볼 수 있다.)
  - 검색 엔진이 `<meta>` 태그에서 해당 웹 사이트의 정보를 크롤링 해오기 때문이다.

<br>

### 2) 번들러(Webpack)

그런데 App.js 하나만 가지고 어떻게 그렇게 많은 동적인 동작을 수행할 수 있는지 궁금할 것이다. 실제로 React를 사용하여 프론트엔드 개발을 해본 사람은 알겠지만 index.html과 App.js외에 `.jsx`라는 파일과 많은 `.js`파일들을 사용한다는 것을 알 수 있다.

실제로 index.html에서 로드하는 스크립트는 App.js 하나 뿐이며, 이 App.js에 웹 어플리케이션을 동작시키기 위한 javascript, css, font, image 등을 묶어서 패키징 하는 것이다.

하나로 묶는 이유는 필요한 자원을 요청했을 때, 매 번 여기저기서 가져오는 것보다 경량화하여 묶은 다음 한 번에 전달하는 것이 시간적, 성능상으로 더 이점이 있기 때문이다.

<br>

### 3) 리액트의 Virtual DOM

리액트는 다음과 같은 경우에 리렌더링을 한다.

- props가 변경되었을 때
- state가 변경되었을 때
- forceUpdate() 메서드를 통해 강제 변경했을 때
- 부모 컴포넌트가 렌더링되었을 때 (이 경우 자식까지 전부 다 렌더링됨)

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/03ceebf2-abb0-41c5-bd8f-c5218b5a0c5e)

React는 상태나 속성값에 변경이 있을 경우, 변경된 값을 토대로 Virtual DOM을 그린 후 diffing 알고리즘을 통해 Real DOM과 비교하여 변경 사항만 반영하여 Real DOM에 업데이트하고 리렌더링을 실시한다. 
