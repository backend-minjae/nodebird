antd와 styled-components
------

_app.js와 Head
------

반응형 그리드 사용하기
------
화면을 디자인 하다보면 그리드를 사용하기 마련인데   
적응형 - 모바일,태블릿,데스크탑 각각 나눠서 디자인   
반응형 - 화면크기에따라 디자인이 반응   
xs의 경우 24를 기준으로 나눈다고 보면된다.   
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

로그인 폼 만들기
-----
더미데이터를 만든다
컴포넌트 폴더안에 컴포너늩를 다 넣어논다
컴포넌트에 props로 넘겨주는 함수는 usecallback을 써야 최적화가 되낟
```
  const onSubmitForm = useCallback(() => {
    console.log({
      id, password,
    });
  }, [id, password]);
```

리렌더링
-----
```node.js
// X
<div style={{marginTop : '10px'}}>

<div>
// O

import styled from 'styled-components';

const ButtonWrapper = styled.div'
margin-top : 18px
';
<ButtonWrapper style={{marginTop : '10px'}}>

<ButtonWrapper>

```
리렌더링시 불러오는데
저 객체떄문에 변경사항이 없음에도 불구하고 새로 리렌더링하게함


useMemo = 값을 캐싱
useCallback = 함수를 캐싱
```
const style = useMemo(() => ({marginTop: 10}),[]);
```

더미데이터로 로그인 하기
------
