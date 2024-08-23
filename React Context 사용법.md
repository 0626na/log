이 메모에서는 React context의 실제 사용방법을 서술합니다. 서술하는 예제코드는 라이트 모드, 다크모드 설정을 context를 이용해서 전역적으로 관리하는 방법입니다.

먼저 context를 생성합니다.

```tsx
import React, { createContext, useState } from 'react';

// ThemeContext 생성
// context를 만드는 함수는 createContext 입니다.
export const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  return (
  // context를 사용하는 코드에 값을 지정하고 싶을경우 생성한 context이름 뒤에 Provider를 붙여서 태그를 만들어 줍니다.
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};


```

다음으로는, 위에 만든 Context를 이용하여 테마를 전환하는 컴포넌트 입니다.

```tsx
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

const ThemeToggleButton = () => {
  // 생성한 context를 사용하려면 useContext hook을 이용합니다. 
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button onClick={toggleTheme}>
      현재 테마: {theme === 'light' ? '라이트 모드' : '다크 모드'}
    </button>
  );
};

export default ThemeToggleButton;

```

이제 위에 만든 context와 context를 이용하는 버튼을 가지고 react 애플리케이션을 만듭니다.

```tsx
import React from 'react';
import { ThemeProvider } from './ThemeContext';
import ThemeToggleButton from './ThemeToggleButton';

const App = () => {
  return (
    <ThemeProvider>
      <div>
        <h1>Context API 예제</h1>
        <ThemeToggleButton />
      </div>
    </ThemeProvider>
  );
};

export default App;

```

위에 코드들의 로직을 설명하자면 다음과 같습니다.
1. `ThemeContext`를 생성하고 `ThemeProvider`를 통해 `theme` 상태와 `toggleTheme` 함수를 하위 컴포넌트에 제공한다.
2. `ThemeToggleButton` 컴포넌트에서 `useContext` 훅을 사용하여 `ThemeContext`에 접근하고, 현재 테마를 표시하거나 테마를 전환할 수 있다.
3. `App` 컴포넌트에서는 `ThemeProvider`를 최상위 컴포넌트로 감싸서 모든 하위 컴포넌트가 `ThemeContext`를 사용할 수 있게 한다.


### 같이보면 좋은 내용
[[React Context가 뭔가요?]]
