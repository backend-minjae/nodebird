antd와 styled-components
------
Ant Design [https://ant.design]

기본적으로 버튼 아이콘등 웹개발에 사용하는 것들을 다 준비를 해놓은 사이트   
고객이 사용하는 화면에서는 보통 그대로 가져다 쓰지않고 커스터마이징을 많이해서 쓰고    
보통 어드민 사이트를 사용할때 좋다.   

react에 css를 입힐때   
컴포넌트 자체에 css를 입혀서 컴포넌트를 만든다.   
styled-components - [https://styled-components.com]   
emotion - [https://emotion.sh/docs/introduction]

설치
```
npm i antd styled-components @ant-design/icons
```
아이콘들이 용량을 많이 차지하다보니 최적화 하기 위해서 다른 라이브러리로 분리해놓은 경우가 있으니 따로 설치    


_app.js와 Head
------
pages에 공통적으로 적용되는 걸 처리하기 위해 _app.js를 생성.   
 
```node.js
import React from 'react';
import Head from 'next/head';
import PropTypes from 'prop-types';
import 'antd/dist/antd.css';

const NodeBird = ({ Component }) => {
  return (
    <>
      <Head>
        <title>NodeBird</title>
      </Head>
      <Component />
    </>
  );
};

NodeBird.propTypes = {
  Component: PropTypes.elementType.isRequired,
};

export default NodeBird;
```
props는 번거롭더라도 검증을 해주는게 좋다.

특정 컴포넌트를 공통으로 사용하는것은 레이아웃으로 만들어서 
<AppLayout>
</AppLayout>
처럼 사용.

Head를 수정하고 싶을때는 next에서 헤드를 수정할수있는 Head라는 컴포넌트를 제공.    

반응형 그리드 사용하기
------
무조건 디자인을 따를 필요는 없다    
 
적응형 - 모바일,태블릿,데스크탑 각각 나눠서 디자인   
반응형 - 화면크기에따라 디자인이 반응, 컴포넌트가 재배치된다.

가로부터 자르고 그 다음에 세로를 자른다.   
모바일 -> 데스크탑 순으로 디자인한다.   
xs : 모바일   
sm : 태블릿   
md : 작은데스크탑   
24칸으로 나눠서 채운다고 보면된다.   
그리드 공식문서[https://ant.design/components/grid/]
```node.js
import { Row,Col } from 'antd';
        <Row>
            <Col xs={13} md={6}>
                왼쪽
            </Col>
            <Col xs={11} md={6}>
                {children}
            </Col>
            <Col xs={24} md={6}>
                오른쪽
            </Col>
        </Row>
```
컬럼 사이의 간격을 줄때는 
gutter 를 사용
```node.js
<Row gutter={8}>
</Row>
```
로그인 폼 만들기
-----
서버가 없는 경우는 더미데이터를 만든다   
```node.js
const dummy = {
  nickname: '민제',
  Post: [],
  Followings: [],
  Followers: [],
  isLoggedIn: false,
};

        <Col xs={24} md={6}>
          {dummy.isLoggedIn
            ? <UserProfile />
            : <LoginForm />}
        </Col>
```

리렌더링
-----
스타일에 객체를 집어넣을때 아래처럼 넣게되면   
저 객체떄문에 변경사항이 없음에도 불구하고 새로 리렌더링하게함

```node.js
// X
<div style={{marginTop : '10px'}}>

<div>
// O

import styled from 'styled-components';

const ButtonWrapper = styled.div'
margin-top : 18px
';
<ButtonWrapper>

<ButtonWrapper>

```
위에 처럼 스타일컴포넌트를 사용하고싶지 않을때는 useMemo를 사용할 수 있다.   
값을 캐싱해놓을 수 있음.
```
import React, { useMemo } from 'react';

const style = useMemo(() => ({marginTop: 10}),[]);
<ButtonWrapper style={style}>

<ButtonWrapper>
```
함수형 컴포넌트에서 리렌더링이 될때는 처음부터 끝가지 다시 실행되는게 맞음
배열부분이 바뀌지않는이상 똑같은걸로쳐줌근데 
리턴부분 중에서 지금컴포넌트 이전컴포넌트 비교 다시그림   


더미데이터로 로그인 하기
------
```nodejs
const onSubmitForm = useCallback(() => {
    console.log({
      id, password,
    });
  }, [id, password]);
  
 <Form onFinish={onSubmitForm} style={{ padding: '10px' }}>
```
서브밋이 되면 onFinish 가 호출
