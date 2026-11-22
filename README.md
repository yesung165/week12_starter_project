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


## 3. useRef: .current 속성과 useState의 차이
 
useRef stores a value that stays the same between renders or references to DOM\

- useState uses count, useRef uses ref.current to access values
- useState uses setter (like setCount), useRef is assigned directly with ref.current = to change values
- Re-render, changing state causes a re-render, changing ref.current does not.
- but both keep values on re-render
- useState is for values shown on screen. useRef is for DOM references and internal values, such as timer IDs.

## 4. Code Analysis: useEffect와 useRef 관련 주요 코드 각 1개(5~15줄)를 선택하여 동작 원리 설명

 useEffect(() => {
    // TODO 1: running이 false면 타이머를 만들지 않기
    // TODO 2: running이 true면 setInterval로 1초마다 count 증가
    // TODO 3: Cleanup에서 clearInterval 호출
  }, [running]);



  // TODO 1: 타이머 ID를 유지할 timerRef 생성 (초기값 null)

  const start = () => {
    // TODO 2: timerRef.current에 타이머가 있으면 중복 시작 방지
    // TODO 3: setInterval의 반환 ID를 timerRef.current에 보관
    // TODO 4: 1초마다 count 증가, running을 true로 변경
  };

  const stop = () => {
    // TODO 5: clearInterval로 타이머 중지
    // TODO 6: timerRef.current를 null로, running은 false로 변경
  };

  const reset = () => {
    // TODO 7: stop() 실행 후 count를 0으로 초기화
  };

  useEffect(() => {
    // TODO 8: 언마운트 시 Interval을 정리하는 Cleanup 작성
  }, []);

  not filled out yet, will fill after coding 


### 1. useEffect의 Dependency Array가 필요한 이유는?

- It tells React when the Effect should run again. React compares the values in the array with the previous render and re-runs the Effect only when one of them changed. Without it the Effect runs after every render. In Lab 1 count changes every second, so the timer would be cleaned up and created again every second.
- [] means run once at mount, [running] means run again only when running changes 
- Values used inside the Effect should be in the array if not then the Effect keeps using old values from the first render

### 2. Cleanup을 사용하지 않으면 어떤 문제가 발생할 수 있는가?

- The timer keeps running even after the component unmounts like going to the other lab tavs, and it tries to update state of a component that is gone.
- Every time the Effect runs again, a new timer is added on top of the old one, so timers pile up and count will go up together.
- StrictMode runs it twice, so without cleanup there are already 2 timers from the start.

### 3. useState와 useRef의 가장 중요한 차이는?

- Re-rendering. Changing state with a setter causes a re-render so the screen updates. Changing ref.current does not cause a re-render.
- Both keep their value between renders.
- So useState is for values shown on screen (count, running), and useRef is for values only the code needs like timerID and DOM.

### 4. LAB 2와 LAB 4의 Timer 관리 방식은 어떻게 다른가?

- LAB 2, The timer is controlled by state and useEffect. Buttons only change running, and useEffect( , [running]) creates the timer when running is true and clears it in cleanup when running changes. The timer ID is a local variable inside the Effect.
- LAB 4, The timer is controlled directly in the event handlers. start() calls setInterval themselves, and the timer ID is kept in timerRef.current. useEffect is only used for the cleanup on unmount.
- Duplicate prevention like the timers being added, LAB 2 relies on cleanup and the dependency array, LAB 4 checks timerRef.current !== null before starting.

