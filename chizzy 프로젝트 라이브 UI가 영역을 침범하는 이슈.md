# 라이브 화면이 삐져나온다..

> 사이드 프로젝트 chizzy 개발중에 일어난 이슈와 해결방안에 대한 기록

## 문제

chizzy 프로젝트를 진행하던 도중, 라이브 화면 부분의 높이를 `h-full`로 설정했을 때 부모 요소의 높이를 초과하여 footer 영역을 침범하는 이슈가 발생했다. 이 문제는 부모 요소의 높이가 명확히 설정되어 있음에도 불구하고, 자식 요소 내에 여러 개의 `h-full`이 적용된 요소들이 동시에 부모의 높이를 100%로 채우려 시도하면서 발생했다.

아래는 이슈가 생긴 파일의 코드의 이슈가 발생한 부분이다.

```jsx
<section className="grid grid-cols-[3fr_1fr] gap-4 h-[calc(100vh-120px)] bg-[#121212] ">
  <div className="relative bg-gray-500 h-full box-border">
    <h3>안녕</h3>
    {/* h-full이 설정되어 있기에 다른 요소를 신경쓰지 않고 자신의 높이를 부모요소의 높이 전체를 사용한다. */}
    <div className="bg-black h-full">
      <iframe
        src="https://youtu.be/rjddthcbSxA?si=yPCYd7NAeEp4DbHc"
        allowfullscreen
        className="w-full h-full"
      />
    </div>
  </div>
</section>
```

코드를 보면 iframe을 감싸고 있는 div의 높이와 iframe과 h3를 감싸고 있는 div의 높이가 전부 h-full로 되어 있다. 나는 이렇게 하면 라이브 스트리밍 요소부분의 높이를 전부 차지할거라고 생각하였지만, 실제로 보니 약간 달랐다.

<img height=500 src='./img/chizzy/chizzy%20라이브%20스트리밍%20UI%20높이가%20벗어나는%20이슈.png'>

사진을 보면 라이브 영상 요소가 높이를 초과하여 footer까지 침범한 상태이다.
왜 이렇게 된걸까?

## h-full (height 100%)의 의미를 제대로 파악해보자

CSS에서는 `height: 100%`로 설정하면, 이 설정을 가진 요소가 자신이 속해있는 부모 요소의 높이를 100% 사용한다는 뜻이다. Tailwind에서 `h-full`을 사용하면 부모 요소의 높이를 기준으로 높이를 100% 전부 채운다. 여기서 중요한 점은 부모 요소의 높이가 명확하게 설정되어 있어야 `h-full`이 의도한 대로 동작한다는 것이다. 부모 요소의 높이가 `auto`로 설정되어 있거나 명시적으로 지정되지 않은 경우, 자식 요소의 `h-full`은 제대로 계산되지 않아 예상치 못한 레이아웃 문제가 발생할 수 있다.

여기서는 section 태그에서 명확한 높이가 설정 되어 있기에 h-full은 정상적으로 움직인다.
문제는 부모 요소 내에 존재하는 다른 요소들을 신경 쓰지 않고 `h-full`을 가진 요소가 부모 요소의 높이를 전부 차지하려고 시도한다는 것이다. 실제로 `h3`를 지우고 보면 footer 범위를 침범하지 않고 딱 들어맞는 것을 확인할 수 있었다.

<img height=500 src='./img/chizzy/chizzy 라이브 스트리밍 UI 다른 요소를 지웠을 경우.png'>

## 여러개의 요소를 사용하면서도 높이를 초과하지 않으려면 어떻게 해야하는가?

h-full을 사용할때 요소가 단 하나만 존재하면 문제가 되지 않는다. 하지만 두개 이상의 요소를 사용할때는 다른 요소를 신경쓰지 않고 h-full을 가진 요소가 부모 요소의 높이만큼 차지해버리면서 영역을 침범해 버린다. 이에 대한 해결방법은 여러가지가 있다.

## 해결방법

### flexbox 이용한 레이아웃 조정하기

flexbox를 쓰면 높이에 상관없이 요소들이 자연스럽게 각 요소들이 높이를 조정합니다.

```jsx
<section className="grid grid-cols-[3fr_1fr] gap-4 h-[calc(100vh-120px)] bg-[#121212]">
  {/* h3와 iframe의 부모 요소인 div에 flex와 flex-col을 적용하여 세로방향 flexbox로 설정한다 */}
  <div className="relative bg-gray-500 h-full box-border flex flex-col">
    <h3>안녕</h3>

    {/* flex-grow 설정을 하여 h3다음에 남은 공간을 차지한다. 아런식으로 하면 영역을 침범하지 않으면서 높이를 유연하게 요소마다 적용하는게 가능하다. */}
    <div className="bg-black flex-grow">
      <iframe
        src="https://youtu.be/rjddthcbSxA?si=yPCYd7NAeEp4DbHc"
        allowfullscreen
        className="w-full h-full"
      />
    </div>
  </div>
</section>
```

1. flex flex-col 추가: 부모 div에 Flexbox를 적용하여 자식 요소들이 세로 방향으로 정렬되도록 한다.
2. flex-grow 적용: iframe을 감싸는 div에 flex-grow를 설정하여, h3의 높이를 제외한 나머지 공간을 채우게 한다. 이로 인해 iframe이 부모 요소의 높이를 초과하지 않도록 조정된다.

### grid를 이용한 레이아웃 조정

Grid 레이아웃을 사용하면 행의 높이를 명확히 설정하여 자식 요소들이 부모의 높이를 초과하지 않도록 배치할 수 있다. 특히, `grid-template-rows: auto 1fr;`을 설정하면 첫 번째 행은 콘텐츠의 높이에 따라 자동으로 조절되고, 두 번째 행은 남은 공간을 채우게 된다.

```jsx
<section className="grid grid-cols-[3fr_1fr] gap-4 h-[calc(100vh-120px)] bg-[#121212]">
  {/*부모 요소에 Grid 레이아웃을 설정하고 grid-template-rows를 auto 1fr으로 설정*/}
  <div className="relative bg-gray-500 h-full box-border grid grid-rows-[auto_1fr]">
    {/*첫번째 row인 h3는 auto*/}
    <h3 className="p-4 text-white">안녕</h3>

    {/* 두번째 row인 iframe의 부모 요소는 남은 공간을 1fr로 채움 */}
    <div className="bg-black h-full">
      <iframe
        src="https://www.youtube.com/embed/rjddthcbSxA?si=yPCYd7"
        allowfullscreen
        className="w-full h-full"
      ></iframe>
    </div>
  </div>
  {/* 오른쪽: 기타 콘텐츠 */}
  <div className="bg-gray-700 h-full">오른쪽 콘텐츠</div>
</section>
```

1. grid grid-rows-[auto_1fr] 설정: 부모 div에 Grid 레이아웃을 적용하고, 행의 높이를 auto와 1fr로 설정하여 첫 번째 행은 콘텐츠에 따라 자동으로 높이가 결정되고, 두 번째 행은 남은 공간을 채우도록 한다.
2. h-full 적용: iframe을 감싸는 div에 h-full을 적용하여 Grid의 두 번째 행이 남은 공간을 채우도록 한다. 이로 인해 h3와 iframe이 동시에 부모의 높이를 초과하지 않도록 조정된다.

위의 해결책을 적용하면 이렇게 영역을 초과하지 않고 정상적으로 나옵니다

<img height=500 src='./img/chizzy/chizzy 라이브 스트리밍 UI 높이 벗어나는 이슈 해결.png'>

### CSS calc() 함수 활용

calc() 함수를 사용하여 동적으로 높이를 계산할 수 있다. 예를 들어, h-full 대신 height: calc(100% - [고정 높이]);를 사용하여 특정 요소의 높이를 제외한 나머지 공간을 채우도록 설정할 수 있다. 그리고 h3 태그에 calc에서 빼준 50px만큼 높이값으로 설정합니다.

```jsx
<div className="relative bg-gray-500 box-border h-[calc(100%-50px)]">
  <h3 className="h-[50px]">안녕</h3>
  <div className="bg-black h-full">
    <iframe
      src="https://www.youtube.com/embed/rjddthcbSxA?si=yPCYd7"
      allowfullscreen
      className="w-full h-full"
    ></iframe>
  </div>
</div>
```

## Tailwind CSS의 임의의 값 (arbitrary value)

Tailwind CSS는 기본적으로 다양한 유틸리티 클래스를 제공하지만, 제공하는 것 이외에 요구 사항에 맞춰 커스터마이징이 필요할 때는 **임의의 값(arbitrary value)**을 사용한다. 이때는 대괄호 [ ]를 사용하여 원하는 값을 직접 지정할 수 있다.
예를 들어, 위에서 grid를 사용한 해결방법에서 사용한 grid-rows-[auto_1fr]는 grid-template-rows: auto 1fr;을 적용하는 방법이다.
임의의 값을 사용할 때는 Tailwind의 JIT(Just-In-Time) 모드가 활성화되어 있어야 한다.

## 결론

프로젝트 초기 단계에서 발생한 비교적 단순한 레이아웃 이슈였지만, 이를 통해 CSS 레이아웃의 중요성과 Flexbox 및 Grid의 유연성에 대해 더 깊이 이해하게 되었습니다. 단순히 문제를 해결하고 넘어가는 것이 아니라, 근본 원인을 이해하고 다양한 해결 방안을 적용해보는 것이 중요함을 다시 한 번 확인할 수 있었습니다. 앞으로도 다양한 레이아웃 문제에 직면했을 때, Flexbox와 Grid를 적절히 활용하여 효율적이고 안정적인 UI를 구현할 수 있을 것입니다.
단순 CSS에서 height:100%를 쓰면 어떻게 작동되는지 확실하게 알게 되었습니다.
