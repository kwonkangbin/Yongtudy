# Props

## props란?

pops는 properties의 줄임말입니다. 우리가 어떠한 값들을 컴포넌트에게 전달해줘야 할 때, 이 props를 사용합니다. props를 사용하면 컴포넌트에 props값을 전달해줄 수 있습니다.

## 단일 Props 전달 예시

```js
//App.js
import React from 'react';
import Chicken from './Chicken';

function App() {
    return (
        <Chicken name="큰통치킨"/>
    );
}

export default App;

//Chicken.js
import React from 'react';

function Chicken(props) {
    return <div>치킨은 역시 {props.name}</div>
}

export default Chiken;

//출력 결과
//치킨은 역시 큰통치킨
```

App.js에서 Chicken.js로 props.name을 넘겨주고 Chicken.js에서는 div태그 안에 있는 "치킨은 역시 {props.name}" return해줍니다. 이 때 props는 객체 형태로 전달됩니다.

<br/>

<br/>

## 여러개의 props 전달 예시

```js
//App.js
import React from 'react';
import Chicken from './Chicken';

function App() {
    return (
        <Chicken name="큰통치킨" menu="핫크리스피 치킨" 		color ="orange"/>
    );
}

export default App;


//Chicken.js
import React from 'react';

function Chicken(props) {
    return <div style={{ color: props.color}}>
        치킨은 역시 {props.name}의 {props.menu}지!
    	   </div>
}

export default Chicken;

//출력 결과
//치킨은 역시 큰통치킨의 핫크리스피 치킨지!(orange)
```

이렇게 하면 color, name, menu값까지 받아와서 사용할 수 있다.

**그럼 이걸 좀 더 편하게 쓸 순 없을까?**

**비구조화 할당(구조 분해)** 이란 것을 쓰면 가능하다. 

그럼  `Chicken.js` 코드를 바꿔보자.

```js
import React from 'react';

function Chicken({color, name, menu}) {
    return <div style={{ color}}>
        치킨은 역시 {name}의 {menu}지!
    	   </div>
}

export default Chicken;
```

짜잔 더 간편해졌나..?

<br/>

<br/>

## defaultProps로 default 값 설정해주기

컴포넌트에 defaultProps라는 값을 설정해놓으면  컴포넌트에 props를 사용하지 않았을 때 들어갈 값을 설정할 수 있다. 

```js
//Chicken.js
import React from 'react';

function Chicken({color, name, menu}) {
    return <div style={{ color}}>
        치킨은 역시 {name}의 {menu}지!
    	   </div>
}

Chicken.defaultProps = {
    name: '이상한 치킨집'
}

export default Chicken;
```

이제 `App.js`에서 name이 빠진 Chickien 컴포넌트를 렌더링을 하면 

```js
//App.js
import React from 'react';
import Chicken from './Chicken';

function App() {
    return (
        <Chicken name="큰통치킨" menu="핫크리스피 치킨" 		color ="orange"/>
        <Chicken color="orange", menu="핫크리스피 치킨"/>
    );
}

export default App;

//출력 결과
//치킨은 역시 큰통치킨의 핫크리스피 치킨지!(orange)
//치킨은 역시 이상한 치킨집의 핫크리스피 치킨지!(orange)
```

 <br/>

<br/>

## props.children

컴포넌트 태그 사이에 넣은 값을 가져다 쓰고 싶으면 `props.children`을 사용하면 됩니다. props.children으로 새로운 컴포넌트를 만들어보겠습니다.

Wrapper.js를 맹들어보죠.

```js
//Wrapper.js
import React from 'react';

function Wrapper({ children }) {
    const style = {
        border: '2px solid black'
        padding: '16px'
    };
    
    return (
        <div style= {style}>
        	{children}
        </div>
    )
}

export default Wrapper;

//App.js
import React from 'react';
import Chicken from './Chicken';
import Wrapper from './Wrapper';

function App(){
    return (
      <Wrapper>
          <Chicken name="큰통치킨" menu="핫크리스피 치킨" 		color ="orange"/>
        <Chicken color="orange", menu="핫크리스피 치킨"/>
      </Wrapper>
    );
}

export default App;
```

![image](https://user-images.githubusercontent.com/90879448/193451646-4c111f3d-7a50-457f-9554-6c6678c52956.png)

결과는 이렇게 나올 수 있다. Wrapper 안에 있는 Chicken태그들이 Wrapper안으로 들어온 것을 볼 수 있다.



<br/>

<br/>

<br/>

피드백은 환영입니다!.😶‍🌫️
