# UseState

UseState는 Hook의 기능 중에 하나이고 함수형 컴포넌트에서 상태관리를 가능하게 해주는 좋은 녀석 중에 하나입니다. 그럼 알아볼까요?

<br/>

<br/>

## 단일 사용예제

```js
//useState를 불러와준다.
import React, { useState } from 'react';

function Example() {
    //초기값을 0으로 세팅하고 
    //setCount가 count를 수정해준다.
    const [count, setCount] = useState(0);
    
    return (
    	<>
       	  <p>클릭 횟수 {count}번</p>
          <button onClick={() => setCount(
            count + 1)}>클릭 ㄱ</button>
        </>
    );
}

export default Example;
```

이런식으로 사용할 수 있는데요 이 코드에 경우에는 버튼을 클릭 시에 setConter이 count에 +1을 해주고 p태그 안에 있는 count가 +1씩 올라가게 되는 코드입니다. 

여기서 첫 번째 원소인 counter은 현재 상태를 의미하고 두 번째 원소인 setCounter은 Setter 함수를 의미합니다.

<br/>

<br/>

## setState의 비동기적인 특징

setState는 비동기로 동작한다는 것을 알고계셨나요? 전 몰랐어요 (당연하지 프론트 시작한지 4일됬는데 ㅋㅋ🤕)

아래의 코드를 한 번 볼까요?

```js
import React, { useState } from 'react';

function Example() {
    const [count, setCount] = useState(0);
    
    const onClick = () => {
        setCount(count + 1);
        setCount(count + 1);
        setCount(count + 1);
    }
    
    return (
    	<>
       	  <p>클릭 횟수 {count}번</p>
          <button onClick={onClick}>클릭 ㄱ</button>
        </>
    );
}

export default Example;
```

결과값이 어떻게 나올까요?? 두구두구두구두구

**바로바로 1입니다!** (엥?🤔)

왜일까요? 바로 setState의 비동기적 특징 때문입니다. 리액트는 렌더링을 효율적으로 하기 위해서 여러 개의 상태 변화 요청을 batch 처리합니다. 만약 여러개의 setState를 실행할 때마다  리렌더링이 일어나면 ..... 생각만 해도 어지럽습니다. 그래서 setState는 batch처리합니다.

> batch란? batch는 일괄처리를 뜻하며 한 번에 모아서 처리하는 것을 의미합니다.

그럼 값이 3이 나오게 하려면 어떻게 해야하나요?

아래에서 알아보죠 아래로 ㄲ!

<br/>

## 함수형 업데이트

함수형 업데이트를 사용해봅시다. 앞서서 setState의 비동기적인 특징을 살펴보았는데요. 함수형 업데이트를 사용하면 동기적으로 setState를 실행시킬 수 있습니다.

코드를 보죠

```js
import React, { useState } from 'react';

function Example() {
    const [count, setCount] = useState(0);
    
    const onClick = () => {
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
    }
    
    return (
    	<>
       	  <p>클릭 횟수 {count}번</p>
          <button onClick={onClick}>클릭 ㄱ</button>
        </>
    );
}

export default Example;
```

이렇게 하면 버튼을 한 번 눌렀을 때 count의 값에는  3이 나오게 되겠죠. 왜냐 전에 연산했던 내용을 저장해서 그 다음 연산을 진행하기 때문이죠!! 함수형 업데이트는 업데이트된 값을 저장합니다. 즉, 여러번의 업데이트가 적용된 후에 다음 렌더링에서 사용된다는 것이죠.

<br/>

<br/>

피드백은 환영입니다!🤔

