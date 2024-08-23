---

---
React에선 컴포넌트간에 데이터를 주고 받을때 props를 이용합니다. 그런데 컴포넌트의 수가 적을때는 괜찮지만, 다수의 컴포넌트간에 데이터를 서로 전달하는 경우에는 어려움이 있습니다. 어떤 컴포넌트에서 실제로는 사용하지 않지만 더 아래 계층에 있는 컴포넌트에게 데이터를 전달하려고 props를 받아서 넘겨주기만 하는 경우가 있습니다. 이렇게 props를 계속 받아서 넘기고 넘기고 하는 안좋은 상황이 발생할수 있습니다. 이러한 현상을 props drilling이라고 합니다.
Context는 데이터를 상위에서 하위로 징검다리 처럼 건네주는 방식이 아닌, 계층에 상관없이 자유롭게 데이터에 접근하기 위해 생겨난 React에서 데이터를 global하게 다룰 목적으로 만든 기능입니다.


![prop drilling](https://react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fpassing_data_prop_drilling.dark.png&w=640&q=75)


위에 그림은 props drilling을 그림으로 나타낸 것입니다. context는 이렇게 깊게 계속 props로 들어가야 하는 경우에 유용합니다.

### 더 자세히 알고싶으면...
- [리액트 공식문서 Context Part](https://react.dev/learn/passing-data-deeply-with-context)

### 실제 Context 사용하는 방법은?
[[React Context 사용법]]

#react #react-context 

