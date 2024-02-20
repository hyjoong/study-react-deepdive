# 6장 ~ 7장 디버깅

# 리액트 개발도구로 디버깅하기

웹서비스 개발뿐만 아니라 모든 프로그래밍에서 중요한 것이 디버깅이다. 리액트로 개발된 애플리케이션의 디버깅을 돕기 위한 도구를 활용해 무엇을 디버깅할 수 있고, 어떤 정보를 확인할 수 있는지 알아보자.

## 리액트 개발 도구

리액트 팀은 원활한 개발을 위해 react-dev-tools를 만들어 제공하고 있다. 

### 컴포넌트 탭

Components 탭에서는 현재 리액트 애플리케이션의 컴포넌트 트리를 확인할 수 있다. 단순히 컴포넌트의 구조뿐만 아니라 props와 내부 hooks 등 다양한 정보를 확인할 수 있다.

컴포넌트 트리에 보여지는 컴포넌트들은 어떻게 선언되었는지에 따라 이름이 부여되거나 익명으로 보여진다. 함수 표현식이나 함수 선언식으로 표기한 경우 함수의 이름이 컴포넌트 이름으로 부여된다. 이를 제외한 모든 컴포넌트는 Anonymous라는 이름으로 보여진다.

익명 함수를 export defualt 한 경우 컴포넌트 탭에는 _default라는 이름으로 보여진다. 또한 memo를 이용해 익명 함수로 만든 컴포넌트를 감싼 경우 역시 Anonymous라는 이름으로 표기된다.

컴포넌트를 기명 함수로 선언하는 것은 개발 도구에서 확인하는 데 많은 도움을 준다. 이름을 부여하기 어려운 경우 함수 `display` 속성을 통해 컴포넌트 탭에서 설정한 이름으로 보여줄 수 있다.

물론 개발 모드가 아닌 빌드된 리액트 트리를 확인하는 경우 난수화되어 있기 때문에 확인하기 어렵다.

컴포넌트를 선택하면 해당 컴포넌트에 대한 자세한 정보를 확인할 수 있으며 다음과 같은 정보를 제공한다.

- 컴포넌트 명과 key
- 컴포넌트 도구
    - 컴포넌트 도구에는 총 세개의 아이콘이 있다.
    - 눈 모양 아이콘을 누르면 해당 컴포넌트가 HTML 어디에서 렌더링됐는지 확인할 수 있다.
    - 벌레 모양 아이콘을 클릭하면 콘솔 탭에 해당 컴포넌트의 정보가 출력된다. 여기에는 해당 컴포넌트가 받는 props, hooks, 해당 컴포넌트의 HTML 요소인 nodes가 기록된다.
    - 소스코드 아이콘을 클릭하면 해당 컴포넌트의 소스코드를 확인할 수 있다. 프로덕션 모드에서 빌드되어 압축된 형태의 코드를 볼 수 있다. 좌측 하단 `{}` 아이콘을 눌러 난독화된 코드를 펼쳐 볼 수 있다.
- 컴포넌트 props
    - 해당 컴포넌트가 받은 props를 확인할 수 있는 영역이 있다. 단순한 원시값 뿐만 아니라 함수도 포함되어 있다.
    - 값이 함수인 props를 클릭하면 Go to definition을 통해 함수가 선언된 코드로 이동할 수 있다.
- 컴포넌트 hooks
    - 컴포넌트에서 사용 중인 훅 정보를 확인할 수 있는데, use가 생략된 이름인 State와 같은 형태로 나타낸다.
    - 커스텀 훅 역시 표현되고 커스텀 훅에서 사용하는 훅이 계층적으로 표현된다.
    - 콜백 함수가 있는 훅과 같은 경우 함수의 이름을 설정해 주면 개발 도구에서 확인할 수 있다.
- 컴포넌트를 렌더링한 주체, rendered by
    - rendered by를 통해 해당 컴포넌트를 렌더링한 주체가 누구인지 확인할 수 있다. 프로덕션 모드에서는 react-dom의 버전만 확인할 수 있지만 개발 모드에서는 해당 컴포넌트를 렌더링한 부모 컴포넌트까지 확인할 수 있다.

### 프로파일러 탭

컴포넌트 매뉴가 정적인 현재 리액트 컴포넌트 트리의 내용을 디버깅하기 위한 도구라면 프로파일러는 리액트가 렌더링하는 과정에서 발생하는 상황을 확인하기 위한 도구다. 어떤 컴포넌트가 렌더링됐는지, 또 몇 차례나 렌더링이 일어났으며 어떤 작업에서 오래 걸렸는 지 등 렌더링 과정에서 발생하는 일을 확인할 수 있다.

이 메뉴는 프로덕션 빌드로 실행되는 리액트 애플리케이션에서는 사용할 수 없다.

프로파일러 탭에서는 몇 가지 중요한 설정이 있는데 간단히 알아보자.

- General 탭의 Hightlight updates when components render: 컴포넌트가 렌더링 될 때마다 해당 컴포넌트에 하이라이트를 표시한다.
- Debugging 탭의 Hide logs during second render in Strict Mode: 리액트 애플리케이션이 Strict Mode에서 실행되는 경우, 원할한 디버깅을 위해 useEffect 등이 두 번씩 작동하는 의도적인 작동이 숨겨져 있다. 이때문에 console.log가 두 번 찌기기도 하는데, 이를 막고 싶다면 해당 버튼을 비활성화 하면된다.
- Profiler 탭의 Record why each redered while profiling: 프로파일링 도중 무엇 때문에 컴포넌트가 렌더링됐는지 기록한다.

프로파일링 메뉴는 리액트가 렌더링할 때 어떤 일이 벌어지는지 확인할 수 있는 도구다.

좌측 상단에 위치한 버튼은 프로파일링 시작 버튼으로, 클릭시 프로파일링이 시작된다. 한 번 더 누르면 프로파일링이 종료되고 프로파일링 결과가 나타난다.

두 번째 버튼은 Reload and Start profiling 기능으로 새로고침되면서 동시에 프로파일링이 시작된다. 마찬가지로 다시 누르면 프로파일링이 중단되고 결과가 나타난다.

세 번째 버튼은 프로파일링 종료 버튼으로 프로파일링된 현재 내용을 모두 지우는 버튼이다.

네 번째, 다섯 번째 버튼은 각각 프로파일링 테이터를 저장하고 불러오는 버튼이다. 결과 저장시 JSON 파일이 다운로드 되며, 파일을 로딩해 프로파일링 정보를 가져올 수 있다.

상단 불꽃 모양 아이콘 버튼을 클릭하면 Flamegraph를 확인할 수 있다. 렌더 커밋별로 어떠한 작업이 일어났는지 나타낸다. 너비가 넓을 수록 해당 컴포넌트를 렌더링하는데 오래 걸렸다는 것을 의미한다. 당연하게도 오래 걸리는 컴포넌트는 루트 컴포넌트다. 컴포넌트의 구조상 이 그래프 역시 컴포넌트 메뉴와 비슷하게 컴포넌트 트리 구조를 확인할 수 있다. 각 컴포넌트에 마우스 커서를 가져다 대면 해당 컴포넌트의 렌더링과 관련된 정보를 확인할 수 있다.

렌더링되지 않은 컴포넌트는 회색으로 표시되며 Did not reder라는 메시지가 표시된다. 이를 활용하면 제대로 메모이제이션이 작동하고 있는지와 같이 렌더링이 의도적으로 발생하고 있는지 확인하는데 많은 도움이 된다.

옆에 위치한 Ranked 버튼은 해당 커밋에서 렌더링하는 데 오랜 시간이 걸린 컴포넌트를 순서대로 나열한 그래프다.

마지막으로 TimeLine은 시간이 지남에 따라 컴포넌트에서 어떤 일이 일어났는지를 확인할 수 있는 탭이다. 리액트 18 이상 환경에서만 확인 가능하며 상태가 언제 업데이트 되었는지와 같은 과정을 확인할 수 있다.

# 크롬 개발자 도구를 활용한 애플리케이션 분석

리액트에서 발생하는 문제를 디버깅하기 위해서는 리액트 개발 도구로 충분할 수 있지만 애플리케이션에서 발생하는 버그와 디버깅 이슈는 리액트 밖에서 일어날 때도 있다.

일반적인 브라우저 환경에서 발생할 수 있는 문제를 디버깅할 수 있는 도구를 브라우저 개발자 도구라고 한다. 가장 널리 사용되는 크롬 개발자 도구를 통해 어떤 내용을 디버깅 할 수 있는지 알아보자.

## 크롬 개발자 도구란?

웹 페이지에서 일어나는 거의 모든 일을 확인할 수 있는 개발 도구다. 크롬 개발자 도구에서 웹 사이트를 제대로 디버깅하고 싶다면 시크릿 모드에서 페이지와 개발자 도구를 여는 것이 좋다. 브라우저에 설치된 각종 확장 프로그램 때문인데 이 프로그램들이 HTML에 애플리케이션에서 제공하는 정보를 추가할 수 있기 때문이다.

## 요소 탭

이곳에서는 현재 웹 페이지를 구성하고 있는 HTML, CSS 등의 정보를 확인할 수 있다. 요소 탭에서 어떠한 정보를 볼 수 있는지 살펴보자.

### 요소 화면

좌측에는 현재 웹 페이즈를 구성하는 HTML을 나타낸다. 이 중 원하는 태그를 클릭하면 브라우저 페이지의 해당 요소가 강조되고, 태그에 대한 정보를 확인 할 수 있다.

단순히 HTML을 보는 것 뿐만 아니라 직접 코드를 수정해서 웹 페이지에서 어떻게 보이는지 빠르게 확인할 수 있다. 웹 페이지의 DOM을 직접적으로 수정하면 프로덕션으로 개발된 페이제서 수정된 내용이 어떻게 보이는지 미리 확인할 수 있어 편하고, 개발 모드에서도 리액트 코드를 수정해 핫 리로딩을 거치지 않아도 확인할 수 있으므로 빠른 작업이 가능하다.

클래스나 속성값이 동적으로 제어되는 DOM이 있다면 요소의 중단점을 사용해 디버깅할 수 있다. 먼저 디버깅하고 싶은 요소를 선택하고 마우스 오른쪽 버튼을 클릭한 후 중단 위치를 선택하고 중단을원하는 옵션(하위 트리 수정, 속성 수정, 노드 삭제)을 클릭하면 해당 옵션에 대한 동작이 일어났을 때마다 변경을 일으킨 소스코드를 보여준다. 

### 요소 정보

요소 탭 우측에는 선택한 요소에 관련된 정보를 볼 수 있다.

- Styles: 어떤 클래스, 태그명, 아이디 등으로 매핑되어 설정된 스타일인지 확인 할 수 있고, 또한 스타일이 선언돼 있는 파일도 확인할 수 있다.
- Computed: 요소의 크기, 패딩, 보더, 마진과 각종 CSS 적용 결과값을 알 수 있는 탭이다.
- Layouy: CSS 그리드나 레이아웃과 관련된 정보를 확인 할 수 있다.
- EventListener: 현새 요소에 부착된 각종 이벤트 리스너를 확인할 수 있다. 그러나 이벤트 버블링 등으로 이벤트를 발생시키는 경우에는 확인할 수 없다.
- DOM breakpoint: 선택한 요소에 중단점이 있는 지 알려준다.
- Attributes: 해당 요소가 가지고 있는 모든 속성값을 나타낸다.
- 접근성: 웹 이용에 어려운 사람을 위한 기능들이 활용하는 값을 말한다. 접근성에 대한 정보를 확인할 수 있다.

요소 화면이 웹 페이지 요소의 구조를 확인하고, 추가, 수정, 삭제를 할 수 있는 공간이라면 요소 정보 영역에서는 해당 요소와 관련된 모든 정보를 확인할 수 있다. 부라우저에서 해당 DOM이 왜 이런 모습으로 패인팅됐는지, DOM에 추가돼 있는 이벤트 리스너, 접근성과 같은 정보를 확인할 수 있다.

## 소스 탭

소스 탭에서는 웹 애플리케이션을 불러오기 위해 실행하거나 참조된 모든 파일을 확인할 수 있다. JS 파일부터 CSS, HTML, 폰트까지 다양한 파일 정보를 확인할 수 있다.

소스 탭에서는 브레이크 포인트를 생성해 자바스크립트 파일 실행을 중단시키고 디버깅을 수행할 수 있다. 코드에 debugger를 선언하는 것과 동일한 역할을 하지만 소스 코드를 오염시키지 않으므로 더 유용하다.

소스 탭 오른쪽에서 제공하는 정보와 기능은 무엇인지 살펴보자

- watch: 감시하고 싶은 변수를 선언하고, 해당 변수의 정보를 확인할 수 있는 메뉴다. 디버깅 시점에서 특정 변수의 값을 알고 싶을때 사용한다.
- Breakpoint: 현재 웹사이트에서 추가한 중단점을 확인할 수 있다.
- Scope: 현재 중단점에서의 스코프를 의미한다. 로컬은 로컬 스코프를 의미하고 클로저, 전역 스코프 등을 확인할 수 있다.
- Call stack: 현재 중단점의 콜스택을 확인할 수 있다. 자바스크립트 코드가 실행되며 생성되는 실행 콘텍스트가 어떻게 저장되어 현재 어떤 모습을 하고 있는지 직접 볼 수 있다.
- Global Listener: 현재 전역 스코프에 추가된 리스너 목록을 확인할 수 있다.
- XHR/fetch, DOM, EventListener, CSP Violation 등 다양한 중단 점을 확인할 수 있다.

## 네트워크 탭

네트워크 탭에는 해당 웹 페이지를 접속하는 순간부터 발생하는 모든 네트워크 관련 작동이 기록된다. HTTP 요청부터 웹 소켓에 이르기까지, 웹 페이지가 외부 데이터와 통신하는 정보를 확인할 수 있다. 보고 싶은 네트워크 종류를 필터링할 수도 있다.

하단에는 페이지를 불러오는 기간 동안 발생한 총 요청 건수와 총 다운로드한 업로드 리소스의 크기를 확인할 수도 있다.

네트워크 탭에서 집중적으로 확인해 봐야 하는 점은 다음과 같다.

- 불필요한 요청, 중복되는 요청이 없는지
- 웹페이지 구성에 필요한 리소스 크기가 너무 크지 않은지
- 리소스를 불러오는 속도는 적절한지 또는 너무 속도가 오래 걸리는 리소스는 없는지
- 리소스가 올바른 우선순위로 다운로드되어 페이지를 자연스럽게 만들어 가는지

## 메모리 탭

현재 웹 페이지가 차지하고 있는 메모리 관련 정보를 확인할 수 있다. 애플리케이션에서 발생하는 메모리 누수, 속도 저하, 웹 페이지 프리징 현상을 확인할 수 있다.

메모리 탭 그 자체만으로는 아무것도 할 수 없고, 프로파일링 작업을 거쳐야 한다.

- 힙 스냅샷: 메모리 상황을 사진 찍듯이 기록할 수 있다. 현재 시점의 메모리 상황을 알고 싶을 때 사용한다.
- 타임라인의 할당 계측: 시간의 흐름에 따라 메모리 변화를 살펴보고 싶을때 사용한다. 주로 로딩되는 과정의 메모리 변화, 상호작용을 했을 때 메모리 변화 과정을 알고 싶을 때 사용한다.
- 할당 샘플링: 메모리 공간을 차지하고 있는 자바스크립트 함수를 볼 수 있다.

### 자바스크립트 인스턴스 VM 선택

현재 실행중인 자바스크립트 VM 인스턴스를 확인할 수 있는데, 디버깅하고 싶은 자바스크립트 VM환경을 선택한다. 그리고 실제 해당 페이지가 자바스크립트 힙을 얼마나 점유하고 있는지 나타낸다.

### 힙 스냅샷

현 시점의 메모리 상황을 볼 수 있다. 스냅샷 수행이 완료되면 애플리케이션에 할당된 메모리 정보를 확인할 수 있다.

스냅샷을 두번 찍은뒤 모든 객체 메뉴를 클릭해 스냅샷 1에서 스냅샷 2사이에 할당된 객체를 클리하고 얕은 크기 항목을 기준으로 내림차순 정렬하면 어떤 변수가 메모리를 많이 차지하고 있는지 확인할 수 있다. 어떤 함수가 실행되어 메모리가 많아졌는지, 전역 변수로 설정해 어떤 값을 가지고 있는지도 직접 확인할 수 있다.

### 타임라인 할당 계측

시간의 흐름에 따라 메모리 변화를 확인할 수 있는 기능이다. 프로파일링을 마치면 메모리 변화를 시간의 흐름에 따라 볼 수 있다.  따라서 메모리 변화를 일으킨 변수가 무엇인지, 얼마나 메모리를 차지하고 있는지 등을 확인할 수 있다.

### 할당 샘플링

시간 흐름에 따라 발생하는 메모리 점유를 확인할 수 있다는 점에서 타임라인 계측과 비슷하지만 자바스크립트 실행 스택별로 분석할 수 있고, 이 분석을 함수 단위로 한다는 차이점이 있다.

할당 샘플링을 무거운 순으로 정렬하면 가장 많은 메모리를 차지한 함수의 작업이 맨 위로 올라오기 때문에 어떤 함수에서 많은 메모리를 발생시켰는지 확인할 수 있다.