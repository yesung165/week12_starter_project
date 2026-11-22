# OSS Week 12 — React Hooks Playground (Student Starter)

## 실행 방법
Node.js 20.19+ 또는 22.12+ 권장. 프로젝트 폴더에서 실행:

```bash
npm install
npm run dev
```

브라우저에 터미널의 Local URL을 열고 LAB 탭을 이동하세요. 제출 전 `npm run build`도 실행하세요.

## 수행 방법
1. `src/components/` 아래의 LAB 1~4 파일에 있는 `TODO`를 직접 완성합니다.
2. `src/App.jsx`, `src/App.css`는 공통 화면이므로 가급적 수정하지 않습니다.
3. 각 LAB의 동작 화면과 콘솔 결과를 기록합니다.
4. 한 LAB에서 다른 LAB으로 탭을 전환하면 기존 컴포넌트가 언마운트됩니다. Cleanup 결과를 관찰합니다.


## 이해도 확인 질문
1. `setCount(prev => prev + 1)`은 왜 유용한가?
2. `useEffect(fn, [])`과 `useEffect(fn)`은 어떻게 다른가?
3. Cleanup은 언제 실행되는가? 개발 환경 StrictMode 영향은?
4. `useState`와 `useRef`의 값 변경 시 렌더링 차이는?
5. LAB 2와 LAB 4에서 Timer를 관리하는 방식의 차이는?

# Source Code Analysis
 
## 1.Project Structure: App.jsx와 주요 Lab 컴포넌트의 역할
 
- src/main.jsx: renders <App/> into #root. The app is wrapped in React.StrictMode, so in development every Effect's setup and cleanup runs one extra time. 
- src/App.jsx: The shared screen that shows the 4 labs as tabs. The labs array holds each lab's id, name, and component, and useState keeps track of the active tab. Only the selected lab is rendered, so switching tabs unmounts the previous lab and runs cleanap
- Lab1_EffectTimer.jsx: Uses useEffect and setInterval to build a counter that goes up by one every second. For observing cleanup and comparing how often the Effect runs with [] 
- Lab2_TimerControl.jsx: Connects state and Effect to control the timer.State handles Start,Stop,Reset, and useEffect(..., [running]) creates and cleans up the timer whenever running changes.
- Lab3_InputFocus.jsx: Uses useRef to access the real input DOM element and use the Focus and Clear buttons.
- Lab4_RefTimer.jsx: Improves the Lab 2 timer by storing the timer ID in a useRef and controlling the timer directly from the Start Stop Reset button handlers.

## 2. useEffect: Effect, Dependency Array 및 Cleanup의 동작 원리

- Render, commit Effect, Render changes UI, commit changes the real DOM and Effect syncs with the outside sytem. 

Effect
- Things that interact outside the system in react, such as timers, browser events, and API requests. If there is nothing to sync with outside React, useEffect is not needed.

Dependency Array: Decides when the Effect runs again.
- useEffect(fn, []) runs only once, at the first mount.
- useEffect(fn) with no array runs every time the component commits. In Lab 1, count changes every second, so the Effect also re-runs every second instead of once.

Cleanup
- It cleans up what the previous Effect did. It runs right before the Effect runs again because a dependency changed, and when the component unmounts.The timer keeps running after the component is gone and timers can pile up if no cleanup.