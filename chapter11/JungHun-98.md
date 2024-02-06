# 11장 Next 13과 리액트 18

## app 디렉터리

현재까지 Next.js의 아쉬운 점으로 평가받던 것 중 하나는 바로 레이아웃의 존재다. React-router-dom을 활용하면 <Router> 바깥에 공통 컴포넌트를 정의하고 사용하기 쉽다. 이를 Next에서 구현하려면 어떻게 해야 할까? 13 버전 이전까지 모든 페이지는 각각의 물리적으로 구별된 파일로 독립돼 있었다. 페이지에 공통으로 무언가 넣으려면 _app, _document가 유일했지만 이 파일들은 서로 다른 목적을 가지고 있다.

- _document: 페이지에서 쓰이는 <html>, <body> 태그를 수정하거나 서버 사이드 렌더링 시 styled-componetns 같은 기술을 지원하기 위한 코드를 삽입하는 제한적인 용도로 사용된다. 오직 서버에서 동작하므로 Web API와 같은 기능은 사용할 수 없다.
- _app: 페이지를 초기화하기 위한 용도로 사용되며 가능한 작업이 명시적으로 정해져 있다.
    - 페이지 변경 시 유지하고 싶은 레이아웃 유지
    - componentDidCatch를 활용한 에러 핸들링
    - global css 주입

즉, 12버전 이전은 _app이 유일하게 페이지의 공통 레이아웃을 유지할 수 있도록 해줬다. 하지만 각 페이지 별로 서로 다른 레이아웃을 유지할 수 있는 여지가 부족했다.

### 라우팅

기존 /pages로 정의하던 라우팅 방식이 /app 디렉터리로 이동했고 파일 명으로 라우팅하는 것이 불가능해졌다.

**라우팅 정의하기**

기본적으로 Next.js의 라우팅은 파일 시스템을 기반으로 하고 있다. app 기반 라우팅 시스템은 기존 /pages를 사용했던 것과 비슷하지만 약간의 차이가 있다.

12버전 이전에는 /pages/a/b.tsx 또는 /pages/a/b/index.tsx는 모두 동일한 주소로 변환되었다. 즉 파일명이 index라면 이 내용은 무시된다. 13버전의 app 라우터에서는 app/a/b가 /a/b로 변환되며 파일명은 무시되고 폴더명까지만 주소로 변환된다.

**layout.js**

Next 13부터는 app 디렉터리 내부의 폴더명이 라우팅 되며, 이 폴더에 포함될 수 있는 파일명이 몇 가지 정해져 있다. 그중 하나가 layout.js다. 이 파일은 페이지의 기본적인 레이아웃을 구성하는 요소다. 해당 폴더에 layout.js가 있다면 그 하위 폴더 및 주소에 모두 영향을 미친다.

루트에는 단 하나의 layout을 만들 수 있으며 이 layout은 모든 페이지에 영향을 미치는 공통 레이아웃이다. 일반적으로 웹 페이지를 만드는데 필요한 공통적인 내용(html, head 등)을 다루는 곳으로 보면된다. 페이지 하위에 추가되는 layout는 해당 주소 하위 라우팅에서만 적용된다.

이 공통 코드는 모든 애플리케이션에 영향을 미치지 않고 오로지 자신과 자식 라우팅에만 미치게 되므로 하나의 애플리케이션에서 레이아웃을 더욱 유연하게 구성할 수 있게 됐다.

layout에서 주의해야 할 점은 다음과 같다.

- layout은 app 디렉터리 내에선 예약어이다. 레이아웃 이외의 다른 목적으로는 사용할 수 없다.
- layout은 children을 props로 받아서 렌더링해야 한다. 레이아웃이므로 그려야 할 컴포넌트를 외부에서 주입받아야 한다.
- layout 내부에는 반드시 export defualt로 내보내는 컴포넌트가 있어야한다.
- layout 내부에서도 API 요청과 같은 비동기 작업을 수행할 수 있다.

**page.js**

layout과 마찬가지로 예약어이며 Next에서 일반적으로 다뤘던 페이지를 의미한다. page.js의 리액트 컴포넌트는 layout을 기반으로 노출되며, page에서 받는 props는 다음과 같다.

- params: 옵셔널 값으로 동적 라우트 파라미터를 사용할 경우 해당 파라미터에 값이 들어온다.
- searchParams: URL에서 ?a=1과 같은 searchParams를 의미한다. key와 value가 자바스크립트 객체 값이 오게된다. 이 값은 layout에서 제공되지 않는데 layout은 페이지 탐색 중에는 리렌더링을 수행하지 않기 때문이다. 즉, 같은 페이지 내에서 쿼리 파라미터만 다르게 라우팅을 시도하는 경우에는 layout의 리렌더링이 불필요하기 때문이다.

page 역시 규칙이 존재한다.

- page는 예약어이므로 레이아웃 이외의 다른 목적으로 사용할 수 없고 내부에 반드시 export default로 내보내는 컴포넌트가 있어야한다.

**error.js**

해당 라우팅(디렉토리) 영역에서 사용되는 공통 에러 컴포넌트다. 이를 이용하면 특정 라우팅 별로 서로다른 UI의 error를 렌더링하는 것이 가능하다. 

error 페이지는 에러 정보를 담고 있는 `error:Error` , 객체와 에러 바운더리를 초기화 할 `reset: () => void` 함수를 props로 받는다. 한 가지 명심해야 할 점은 에러바운더리가 클라이언트에서만 작동하므로 error 컴포넌트도 클라이언트 컴포넌트여야 한다는 점이다. 그리고 에러 컴포넌트는 같은 수준의 layout에서 에러가 발생할 경우 해당 에러 컴포넌트로 이동하지 않는다. 레이아웃 위에 에러 UI가 그려지는 것을 막기위함일 것이다.

만약 Layout에서 발생한 에러를 처리하고 싶다면 상위 컴포넌트의 error를 사용하거나 app의 루트 에러처리를 담당하는 페이지를 생성하면 된다.

**not-found.js**

특정 라우팅 하위 주소를 찾을 수 없는 404 페이지를 렌더링 할 때 사용된다.

**loading.js**

loading 컴포넌트를 정의할 수 있고, use client 지시자를 사용해 클라이언트에서 렌더링되게 할 수도 있다.

**route.js**

라우팅 주소를 담당하는 파일이다. route.js 파일 내부에 REST API의 get, post와 같은 메서드명을 예약어로 선언해 두면 HTTP 요청에 맞게 해당 메서드를 호출하는 방식으로 작동한다. route. 파일이 존재하는 폴더 내부에는 page가 존재할 수 없다. 

route 함수들이 받을 수 있는 파라미터는 다음과 같다.

- request: NextRequest 객체이며 fetch의 request를 확장한 next만의 request라고 보면된다.
- contect: params만을 가지고 있는 객체이며, 동적 라우터 파라미터 객체가 포함되어 있다.(key=value의 js 객체 형식)

## 리액트 서버 컴포넌트

## Next.js에서의 리액트 서버 컴포넌트

리액트 18에서 새로 도입된 리액트 서버 컴포넌트는 서버 사이드 렌더링과 완전히 다른 개념이다. 

### 기존 리액트 컴포넌트와 서버 사이드 렌더링의 한계

리액트의 모든 컴포넌트는 클라이언트에서 작동하며, 브라우저에서 자바스크립트 코드 처리가 이뤄진다. 웹 사이트를 방문하면 리액트 실행에 필요한 코드를 다운로드하고 리액트 컴포넌트 트리를 만든 다음, DOM에 렌더링한다.

서버 사이드 렌더링의 경우 서버에서 DOM을 만들어 오고, 클라이언트에서는 이렇게 만들어진 DOM을 기준으로 하이드레이션을 진행한다. 이후 브라우저에서 상태를 추적하고 이벤트 핸들러를 DOM에 추가하며 응답에 따라 렌더링 트리를 변경하기도 한다.

리액트와 Next에서 제공하는 기능은 대략 이러한 흐름이고, 한계점이 존재한다.

- 자바스크립트 번들 크기가 0인 컴포넌트를 만들 수 없다.
    - 클라이언트에서 사용되는 라이브러리나 기능이 많아질 수록 사용자 기기에 부담이 된다.
- 백엔드 리소스에 대한 직접적인 접근이 불가능하다.
- 자동 코드 분할이 불가능하다.
    - 코드 분할이란 하나의 거대한 코드 번들 대신, 코드를 여러 작은 단위로 나누어 필요할 때만 동적으로 지연 로딩함으로서 앱을 초기화하는 속도를 높여주는 기법을 의미한다. 일반적으로 리액트에서는 lazy로 구현해왔다.
- 연쇄적으로 발하는 클라이언트와 서버의 요청을 대응하기 어렵다.
- 추상화 비용이 크다.

이렇게 서버 사이드 렌더링의 한계점을 쭉 살펴본다면 모든 문제는 리액트가 클라이언트 중심으로 돌아가기 때문에 발생하는 문제다. 하지만 서버 사이드 렌더링만으로 서비스를 구현하기에는 리액트의 다양한 사용자 경험 제공의 강점을 포기하기는 어렵다.

결국 서버 사이드 렌더링, 클라이언트 사이드 렌더링은 모두 이 문제를 해결하기에는 조금씩 아쉬움이 있다. 서버 사이드 렌더링은 정적 콘텐츠의 빠른 제공, 서버에 있는 데이터를 쉽게 접근할 수 있지만 인터랙션에 따른 다양한 사용자 경험을 제공하긴 어렵다. 클라이언트 사이드 렌더링은 사용자의 인터랙션에 따라 정말 다양한 것들을 제공할 수 있지만 서버에 비해 느리고 데이터를 가져오는 것도 어렵다. 이러한 두 구조의 장점을 모두 취하고자 하는 것이 **리액트 서버 컴포넌트다.**

### 서버 컴포넌트란?

서버 컴포넌트란 하나의 언어, 하나의 프레임워크, 그리고 하나의 API와 개념을 사용하면서 서버와 클라이언트 모두에서 컴포넌트를 렌더링 할 수 있는 기법을 의미한다. 서버에서 할 수 있는 일은 서버가 처리하고, 서버에서 할 수 없는 클라이언트 작업은 브라우저에서 실행된다. 즉, 일부 컴포넌트는 브라우저에서 일부 컴포넌트는 서버에서 렌더링된다. 여기서 명심해야 할 점은 클라이언트 컴포넌트는 서버 컴포넌트를 임포트 할 수 없다는 것이다.

리액트는 클라이언트 컴포넌트, 서버 컴포넌트, 서버와 클라이언트에서 모두 사용할 수 있는 공용 컴포넌트로 나뉜다. 이들의 차이점을 알아보자

- 서버 컴포넌트
    - 요청이 오면 그 순간 서버에서 딱 한 번 실행될 뿐이므로 상태를 가질 수 없다. useState, useReducer 등의 훅을 사용할 수 없다.
    - 렌더링 생명주기 역시 사용할 수 없다. useEffect, useLayoutEffect를 사용할 수 없다.
    - 앞의 제약사항 때문에 커스텀 훅을 사용할 수 없다. 다만 effect, state에 의존하지 않고 서버에서 제공할 수 있는 기능만 사용하는 훅이라면 충분히 사용 가능하다.
    - DOM API, window, document 등에 접근할 수 없다.
    - DB, 내부 서비스, 파일 시스템 등 서버에 있는 데이터를 비동기로 접근할 수 있다.
    - 다른 컴포넌트, html 요소, 클라이언트 컴포넌트를 렌더링 할 수 있다.
- 클라이언트 컴포넌트
    - 서버 컴포넌트, 서버 전용 훅을 임포트 할 수 없다.
    - 서버 컴포넌트를 자식으로 갖는 것은 가능하다. 서버 컴포넌트는 이미 서버에서 완성된 트리고 클라이언트 컴포넌트는 이미 서버에서 만들어진 트리를 삽입해서 보여주기만 하면 되기 때문이다.
    - 이 두가지만 제외하면 일반적인 리액트 컴포넌트와 비슷하다.
- 공용 컴포넌트
    - 이 컴포넌트는 클라이언트, 서버에서 모두 사용할 수 있으며 제약 사항 역시 클라이언트 컴포넌트, 서버 컴포넌트의 제약 사항을 그대로 받는다.

### 서버 사이드 렌더링과 서버 컴포넌트의 차이

먼저 서버 사이드 렌더링은 응답 받은 페이지 전체를 HTML로 렌더링하는 과정을 서버에서 수행한 후 그 결과를 클라이언트에 내려준다. 이후 하이드레이션 과정을 거쳐 서버의 결과물을 확인하고 이벤트를 붙이는 등의 작업을 수행한다. 따라서 여전히 HTML 로딩 이후 자바스크립트 코드를 다운로드하고 파싱하고 실행하는데 비용이 든다.

서버 컴포넌트를 활용해 서버에서 렌더링할 수 있는 컴포넌트는 서버에서 완성해서 제공 받은 다음, 클라이언트 컴포넌트는 서버 사이드 렌더링으로 초기 HTML으로 빠르게 전달받을 수 있다.

### 서버 컴포넌트는 어떻게 작동하는가?

1. 서버가 렌더링 요청을 받는다. 서버 컴포넌트를 사용하는 모든 페이지는 항상 서버에서 시작된다. 즉, 루트에 있는 컴포넌트는 항상 서버 컴포넌트다.
2. 서버는 받은 요청에 따라 컴포넌트를 JSON으로 직렬화한다. 이때 서버에서 렌더링할 수 있는 것은 직렬화해서 내보내고, 클라이언트 컴포넌트로 표시된 부분은 해당 공간을 placeholder 형식으로 비워두고 나타낸다. 브라우저는 이 결과물을 받아 다시 역직렬화 한 다음 렌더링을 수행한다.
3. 브라우저가 리액트 컴포넌트 트리를 구성한다. 서버로 받은 JSON을 바탕으로 트리를 재구성해 컴포넌트를 만들어 나간다. 클라이언트 컴포넌트를 받았다면 클라이언트에서 렌더링(호출)할 것이고, 서버에서 만들어진 결과물을 받았다면 이 정보를 기반으로 리액트 트리를 그대로 만들것이다. 최종적으로 만들어진 이 트리를 렌더링해 브라우저의 DOM에 커밋한다.

서버에서 클라이언트로 정보를 보낼 때 클라이언트가 줄 단위로 JSON을 읽고 컴포넌트를 렌더링할 수 있어 브라우저에서는 되도록 빨리 사용자에게 결과물을 보여줄 수 있다.

## Next.js에서의 리액트 서버 컴포넌트

Next에서도 기본적인 서버 컴포넌트의 제약은 동일하다. 서버 컴포넌트는 클라이언트 컴포넌트를 불러올 수 없으며, 클라이언트 컴포넌트는 서버 컴포넌트를 childeren props로 받는 것만 가능하다. 

> 서버 컴포넌트는 이미 렌더링된 결과물로 children에 들어갈 것이기 때문
> 

page.js와 layout.js는 반드시 버서 컴포넌트이며, 서버 컴포넌트의 제약을 받는다.

### 새로운 fetch 도입과 getServerSideProps, getStaticProps, getInitialProps의 삭제

과거 Next에서 서버 사이드 렌더링과 정적 페이지 제공을 위해 이용되던 API가 /app 디렉터리 내부에서는 삭제됐다. 대신 모든 데이터 요청은 웹에서 제공하는 fetch API를 기반으로 이뤄진다. 

리액트 팀은 fetch API를 확장해 같은 서버 컴포넌트 트리 내에서 동일한 요청이 있다면 재요청이 발생하지 않도록 요청 중복을 방지했다. 해당 fetch 요청에 대한 내용을 서버에서는 렝더링이 한 번 끝날 때까지 캐싱하며, 클라이언트에서는 별도의 지시자나 요청이 없는 이상 해당 데이터를 최대한 캐싱해서 중복된 요청을 방지한다.

### 정적 렌더링과 동적 렌더링

과거 Next에는 서버에서 불러오는 데이터가 변경되지 않은 경우에 정적으로 페이지를 만들어 제공할 수 있는 기능이 있었다. 이 기능은 해당 주소로 들어오는 경우 모든 결과물이 동일하기 때문에 캐싱해 기존 서버 사이드 렌더링보다 더 빠르데 데이터를 제공할 수 있었다.

Next13에서는 정적인 라우팅에 대해서는 기본적으로 빌드 타임에 렌더링을 미리 해두고 캐싱해 재사용할 수 있게끔 해뒀고, 동적인 라우팅에 대해서는 서버에 요청이 올 때마다 컴포넌트를 렌더링하도록 변경했다.

### 캐시와 mutating, 그리고 revalidating

Next는 fetch의 기본 작동을 재정의할 수 있는데, 이를 바탕으로 해당 데이터의 유효 시간을 정해두고 이 시간이 지나면 다시 데이터를 불러와서 페이지를 렌더링하는 것이 가능하다.

1. 최초로 해당 라우트로 요청이 올 때는 미리 정적으로 캐시해둔 데이터를 보여준다.
2. 이 캐시된 초기 요청은 revalidate에 선언된 값만큼 유지된다.
3. 만약 해당 시간이 지나도 일단은 캐시된 데이터를 보여준다.
4. Next는 캐시된 데이터를 보여주는 한편, 시간이 경과했으므로 백그라운드에서 다시 데이터를 불러온다.
5. 4번의 작업이 성공적으로 끝나면 캐시된 데이터를 갱신하고, 그렇지 않다면 과거 데이터를 보여준다.

### 스트리밍을 활용한 점진적인 페이지 불러오기

과거 서버 사이드 렌더링 방식은 요청 받은 페이지를 모두 렌더링해서 내려줄 때까지는 사용자에게 아무것도 보여줄 수 없다. 그리고 실제 페이지를 다 받았다 하더라도 이 페이지는 사용자가 인터랙션할 수 없는 정적 페이지고, 리액트에서 하이드레이션 과정을 거쳐야만 비로소 사용자가 사용할 수 있는 페이지가 된다. 문제는 이 작업이 순차적으로 완료되어야만 페이지 하나를 온전하게 볼 수 있다. 이 문제를 해결하기 위해 HTML을 작은 단위로 쪼개서 완성되는 대로 클라이언트에 점진적으로 보내는 스트링밍이 도입되었다.

모든 컴포넌트를 기다리는 대신, 컴포넌트가 완성되는 대로 클라이언트에 내려주면 사용자는 페이지가 완성될 때까지 기다리는 지루함을 덜 수 있고, 페이지가 로딩 중이라는 인식을 더 명확하게 심어줄 수 있다.

## 서버 액션

이 기능은 API를 굳이 생성하지 않더라도 함수 수준에서 서버에 직접 접근해 데이터 요청 등을 수행할 수 있는 기능이다.

### form의 action

form은 HTML에서 양식을 보낼 때 사용하는 태그로, action props를 추가해서 이 양식 데이터를 처리할 URI를 넘겨줄 수 있다.

input의 submit과 image의 formAction 역시 서버액션을 추가할 수 있다.

## 정리 및 주의사항

서버 컴포넌트는 파일 단위로 “use client”를 경계로 서버와 클라이언트 컴포넌트를 명확하게 구분해야 하고, 나아가 라이브러리 또한 이 경계에 맞춰서 사용해야 하기 때문에 초기 개발 구조 설계에서 큰 혼동이 있을 것으로 보인다. 

react18, next13 버전은 새로운 기능이 굉장히 많이 추가되었다. create-react-app 수준의 특별한 프레임워크 없이 가벼운 구조로 리액트가 새롭게 제공하는 많은 기능을 완벽하게 사용하는 것은 이제 쉽지 않을 것이다. 단순히 웹팩을 사용할 수 있는 수준을 넘어서 미리 서버 컴포넌트 환경에 잘 맞는 번들러를 추가로 제작해야 하는 수고로움이 동반된다. 일반적인 리액트 개발자들은 리액트의 기초개념을 익히고 리액트 기반 프레임워크가 제공하는 다양한 기능을 섭렵하는 데 초점을 맞춰야 할 것으로 보인다.

리액트 서버 컴포넌트는 이전에 없었던 완전히 새로운 패러다임이며, 앞으로 리액트 생태계에 많은 변화를 가져올 것으로 보인다. 그에 따라 프론트엔드 개발자들에게는 Node.js 서버 환경의 이해가 중요해질 것이다.