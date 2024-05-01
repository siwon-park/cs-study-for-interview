# 00_React 동작 원리



## 1. React(리액트)

리액트는 라이브러리일까? 프레임워크일까?

- 둘 다 해당한다고 보는 게 맞다.
- 라이브러리라는 관점; JS 라이브러리로서 UI 컴포넌트를 구성하고 관리하는데 사용되는 라이브러리다.
- 프레임워크라는 관점; 상태 관리, 라우팅 등을 제공하며 어플리케이션 전체 구조를 정의하고 제어하는 역할을 하기 때문에 프레임워크다.

<br>

## 2. 웹 페이지 렌더링 과정

> HTML + CSS + JS가 웹 페이지를 렌더링 하는 과정

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/e0ac862b-c104-4cd0-8f77-cfb477aeee5a)

```
1. HTML parser가 HTML을 바탕으로 DOM tree를 그린다.
2. CSS parser가 CSS를 바탕으로 CSSOM을 그린다.
3. DOM에 CSSOM을 적용하여 Render Tree를 그린다.
4. Render Tree를 바탕으로 Painting 하여 실제 화면에 렌더링 한다.
*HTML 코드를 읽어 내려가다가 <script></script> 태그를 만나면 파싱을 잠시 중단하고 js 파일을 로드한다.
```

