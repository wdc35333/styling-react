# 9장 컴포넌트 스타일링

리액트에서 컴포넌트를 스타일링할 때는 다양한 방식을 사용 가능하다

이 장에서는 어떠한 방식이 있는지 알아보고, 자주 사용하는 방식을 하나하나 사용해보겠다

스타일링 방식은 다음과 같다

- 일반 CSS: 컴포넌트를 스타일링하는 가장 기본적인 방식
- Sass: 자주 사용되는 CSS 전처리기 중 하나로 확장된 CSS 문법을 사용하여 CSS 코드를 더욱 쉽게 작성할 수 있도록 해줌
- CSS Module: 스타일을 작성할 때 CSS 클래스가 다른 CSS 클래스의 이름과 절대 충돌하지 않도록 파일마다 고유한 이름을 자동으로 생성해주는 옵션
- styled-components: 스타일을 자바스크립트 파일에 내장시키는 방식으로 스타일을 작성함과 동시에 해당 스타일이 적용된 컴포넌트를 만들 수 있게 해줌

실습은 다음 흐름으로 진행

프로젝트 준비하기 -> 일반 CSS 사용하기 -> Sass 사용하기 -> CSS Module 사용하기 -> styled-components 사용하기

## 9.1) 가장 흔한 방식, 일반 CSS

기존의 CSS 스타일링

소규모 프로젝트를 개발하고 있다면 새로운 스타일링 시스템을 적용하는 것이 불필요할 수도 있다

CSS를 작성할 때 가장 중요한 점은 CSS 클래스를 중복되지 않게 만드는 것

CSS 클래스가 중복되는 것을 방지하는 여러 가지 방식중 하나는 이름을 지을 때 특별한 규칙을 사용하여 짓는 것이고, 다른 하나는 CSS Selector를 활용하는 것이다.

### 9.1.1) 이름 짓는 규칙

프로젝트에 자동 생성된 App.css를 읽어보면 클래스 이름이 컴포넌트 이름-클래스 형태로 지어져 있다 (ex: App-header)

클래스 이름에 컴포넌트 이름을 포함시킴으로써 다른 컴포넌트에서 실수로 중복되는 클래스를 만들어 사용하는 것을 방지할 수 있다

비슷한 방식으로 BEM 네이밍이라는 방식도 있다. BEM 네이밍은 CSS 방법론 중 하나로, 이름을 지을 때 일종의 규칙을 준수하여 해당 클래스가 어디에서 어떤 용도로 사용되는지 명확하게 작성하는 방식이다 (ex: .card_title-primary)

### 9.1.2) CSS Selector

CSS Selector를 사용하면 CSS 클래스가 특정 클래스 내부에 있는 경우에만 스타일을 적용할 수 있다

예를 들어 .App 안에 들어 있는 .logo에 스타일을 적용하고 싶다면 다음과 같이 작성하면 됨

```css
.App .logo {
  animation: App-logo-spin infinite 20s linear;
  height: 40vmin;
}
```

```css
.App {
  text-align: center;
}

.App .logo {
  animation: App-logo-spin infinite 20s linear;
  height: 40vmin;
}

@media (prefers-reduced-motion: no-preference) {
  .App-logo {
    animation: App-logo-spin infinite 20s linear;
  }
}

/*  .App 안에 들어 있는 header
    header 클래스가 아닌 header 태그 자체에
    스타일을 적용하기 때문에 .이 생략되었음
*/

.App header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.App a {
  color: #61dafb;
}

.App-link {
  color: #61dafb;
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

```

**App.css**

.App 안에 들어 있는 header, a 태그 자체에 스타일을 적용하기 때문에 .을 생략하였다

이 경우 js안에서 사용할 때

```js
import { Component } from "react";
import logo from "./logo.svg";
import "./App.css";

class App extends Component {
  render() {
    return (
      <div className="App">
        <header>
          <img src={logo} className="logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}

export default App;

```

**App.js**

이런 식으로 컴포넌트의 최상위 html 요소에는 컴포넌트의 이름으로 클래스 이름을 짓고(.App), 그 내부에서는 소문자를 입력하거나(.logo) header 같은 태그를 사용하여 클래스 이름이 불필요한 경우에는 아예 생략할 수 있다

## 9.2) Sass 사용하기

Sass(Syntactically Awsome Style Sheets)(문법적으로 매우 멋진 스타일시트)는 CSS 전처리기로 복잡한 작업을 쉽게 할 수 있도록 해주고, 스타일 코드의 재활용성을 높여 줄 뿐만 아니라 코드의 가독성을 높여서 유지 보수를 더욱 쉽게 해준다

create-react-app 구버전에서는 Sass를 사용하려면 추가 작업이 필요했는데, v2 버전부터는 별도의 추가 설정 없이 바로 사용할 수 있다

Sass에서는 두 가지 확장자 .scss와 .sass를 지원한다. Sass가 처음 나왔을 때는 .sass 확장자만 지원되었으나 나중에 개발자들의 요청에 의해 .scss 확장자도 지원하게 되었다.

.scss의 문법과 .sass의 문법은 꽤 다르다. 아래의 코드에서 확인

```sass
$font-stack: Helvetica, sans-serif
$primary-color: #333

body
	font: 100% $font-stack
	color: $primary-color
```

```scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

주요 차이점을 살펴보면 .sass 확장자는 중괄호({})와 세미콜론(;)을 사용하지 않는다 반면 .scss 확장자는 기존 CSS를 작성하는 방식과 비교해서 문법이 크게 다르지 않다. 보통 .scss 문법이 더 자주 사용된다

Sass를 한 번 사용해보자
