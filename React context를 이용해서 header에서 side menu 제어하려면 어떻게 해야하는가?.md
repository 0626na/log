
개인 프로젝트 짭치직에의 메인화면을 개발중이다. header, sidebar라는 다른 두개의 컴포넌트가 있다. header에는 햄버거 버튼이 존재한다. 이 버튼으로 사이드바 메뉴의 width 크기를 늘리고 줄이는 기능을 제어하고 싶다. 그런데 next.js에서 rootLayout은 서버 컴포넌트이다. layout에 표시하려면 두가지 방법이 있다.

첫번째는 header와 sidebar를 하나의 파일에 import 하고, 이 파일을 layout에 import 한다. 그리고 header와 sidebar가 import된 파일을 client component로 만들어서 state를 내려주면 된다.

두번째는 header 컴포넌트에서의 햄버거 버튼을 클릭 상태를 글로벌로 관리하면 된다.
이건 세번째 테스트