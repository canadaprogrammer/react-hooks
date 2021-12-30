# React Hooks

- Hooks let you use state and other React features without writing a class.

- Hooks are backwords-compatible.

- Hooks are JavaScript functions, but they impose two additional rules:

  - Only call Hooks **at the top level**.

  - Only call Hooks from React function components.

## Difference between `useState()` with a function and `state` with a class

- ```jsx
  import React, { StrictMode } from 'react';
  import ReactDOM from 'react-dom';
  import { useState } from 'react';
  import './styles.css';

  // using `useState()`
  const App = () => {
    const [item, setItem] = useState(0);
    const increaseItem = () => setItem((prev) => prev + 1);
    const decreaseItem = () => setItem((prev) => prev - 1);
    return (
      <div className='App'>
        <h1>Item {item}</h1>
        <button onClick={increaseItem}>Increase</button>
        <button onClick={decreaseItem}>decrease</button>
      </div>
    );
  };

  // using `state`
  class AppUgly extends React.Component {
    state = {
      item: 0,
    };
    render() {
      const { item } = this.state;
      return (
        <div className='App'>
          <h1>Item {item}</h1>
          <button onClick={this.increaseItem}>Increase</button>
          <button onClick={this.decreaseItem}>decrease</button>
        </div>
      );
    }
    increaseItem = () =>
      this.setState((state) => {
        return {
          item: state.item + 1,
        };
      });
    decreaseItem = () =>
      this.setState((state) => {
        return {
          item: state.item - 1,
        };
      });
  }

  const rootElement = document.getElementById('root');
  ReactDOM.render(
    <StrictMode>
      <App />
      <AppUgly />
    </StrictMode>,
    rootElement
  );
  ```

## `useInput()`

- ```jsx
  const useInput = (initialValue, validator) => {
    const [value, setValue] = useState(initialValue);
    const onChange = (event) => {
      const {
        target: { value }
      } = event;
      let willUpdate = true;
      if (typeof validator === "function") {
        willUpdate = validator(value);
      }
      if (willUpdate) {
        setValue(value);
      }
    };
    return { value, onChange };
  };

  const App = () => {
    const maxLen = (value) => value < 10;
    const name = useInput("Mr. ", maxLen);
    return (
      <div className="App">
        <h1>Hello</h1>
        {/* <input placeholder="Name" value={name.value} onChange={name.onChange} /> */}
        {/* USE SPREAD SYNTAX(...)}
        <input placeholder="Name" {...name} />
  ```

## `useTabs()`

- ```jsx
  const content = [
    {
      tab: "Section 1",
      content: "I'm the content of the Section 1"
    },
    {
      tab: "Section 2",
      content: "I'm the content of the Section 2"
    }
  ];

  const useTabs = (initialTab, allTabs) => {
    const [currentIndex, setCurrentIndex] = useState(initialTab);
    if (!allTabs || !Array.isArray(allTabs)) {
      return;
    }
    return {
      currentItem: allTabs[currentIndex],
      changeItem: setCurrentIndex
    };
  };
  const App = () => {
    const { currentItem, changeItem } = useTabs(0, content);
    return (
      <div className="App">
        {content.map((section, index) => (
          <button onClick={() => changeItem(index)}>{section.tab}</button>
        ))}
        <div>{currentItem.content}</div>
  ```

## `useEffect(effect(fn), dependency([]))`

- `useEffect(fn);` is similar to componentDidMount and componentDidUpdate

  - `useEffect()` runs both after the first render and after every update.

- `useEffect(fn, []);` is similar to componentDidMount

  - `useEffect()` runs only after the first render.

- `useEffect(fn, [dependencies]);` is similar to componentDidMount and componentDidUpdate with dependencies

  - `useEffect()` runs after the first render and after the dependencies changed.

- If the `fn` has `return fn`, the return function cleans up the effect. It's similar to componentWillUnmount

## `useTitle()`

- ```jsx
  const useTitle = (initialTitle) => {
    const [title, setTitle] = useState(initialTitle);
    const updateTitle = () => {
      const htmlTitle = document.querySelector('title');
      htmlTitle.innerText = title;
    };
    useEffect(updateTitle, [title]);
    return setTitle;
  };

  const App = () => {
    const titleUpdater = useTitle('Loading...');
    setTimeout(() => titleUpdater('Home'), 1000);
    return (
      <div className='App'>
        <div>Hi</div>
      </div>
    );
  };
  ```

## `useRef()`

- ```jsx
  import { useRef } from 'react';

  const App = () => {
    const reference = useRef();
    // setTimeout(() => console.log(reference.current), 1000); // <input placeholder="la"></input>

    // for preventing an error of `focus` related to undefined earlier
    // Method 1: add `?`
    setTimeout(() => reference.current?.focus(), 1000);
    // Method 2: use `useEffect()`
    useEffect(() => {
      setTimeout(() => reference.current.focus(), 1000);
    }, []);
    return (
      <div className="App">
        <div>Hi</div>
        <input ref={reference} placeholder="la" />
  ```

## `useClick()`

- ```jsx
  const useClick = (onClick) => {
    const element = useRef();
    useEffect(() => {
      if (typeof onClick !== "function") {
        return;
      }
      const { current } = element;
      if (current) {
        current.addEventListener("click", onClick);
      }
      // clean up
      return () => {
        if (current) {
          current.removeEventListener("click", onClick);
        }
      };
    }, []);
    return typeof onClick !== "function" ? undefined : element;
  };
  function App() {
    const sayHello = () => console.log("Hello");
    const title = useClick(sayHello);
    return (
      <div className="App">
        <h1 ref={title}>Hi</h1>
  ```

## `useConfirm()`

- ```jsx
  const useConfirm = (message = "", onConfirm, onCancel) => {
    if (typeof onConfirm !== "function") {
      return;
    }
    if (typeof onCancel !== "function") {
      return;
    }
    const confirmAction = () => {
      if (window.confirm(message)) {
        onConfirm();
      } else {
        onCancel();
      }
    };
    return confirmAction;
  };
  const App = () => {
    const deleteWorld = () => console.log("Deleting the world...");
    const abort = () => console.log("Aborted");
    const confirmDelete = useConfirm("Are you sure?", deleteWorld, abort);
    return (
      <div className="App">
        <button onClick={confirmDelete}>Delete the world</button>
  ```

## `usePreventLeave()`

- ```jsx
  const usePreventLeave = () => {
    const listener = (event) => {
      event.preventDefault();
      event.returnValue = "";
    };
    const enablePrevent = () => window.addEventListener("beforeunload", listener);
    const disablePrevent = () =>
      window.removeEventListener("beforeunload", listener);
    return { enablePrevent, disablePrevent };
  };
  const App = () => {
    const { enablePrevent, disablePrevent } = usePreventLeave();
    return (
      <div className="App">
        <button onClick={enablePrevent}>Protected</button>
        <button onClick={disablePrevent}>Unprotected</button>
  ```

## `useBeforeLeave()`

- ```jsx
  const useBeforeLeave = (message) => {
    useEffect(() => {
      if (typeof message !== "function") {
        return;
      }
      const handle = (event) => {
        const {clientY} = event;
        if(clientY <= 0) {
          message();
        }
      };
      document.addEventListener("mouseleave", handle);
      return () => document.removeEventListener("mouseleave", handle);
    }, []);
  };

  const App = () => {
    const message = () => window.alert("please don't leave!!");
    useBeforeLeave(message);
  ```

## `useFadeIn()`

- ```jsx
  const useFadeIn = (duration = 1, delay = 0) => {
    const element = useRef();
    useEffect(() => {
      if (typeof duration !== "number" && typeof delay !== "number") {
        return;
      }
      if (element.current) {
        const { current } = element;
        current.style.transition = `opacity ${duration}s ease-in ${delay}s`;
        current.style.opacity = 1;
      }
    }, [duration, delay]);
    return { ref: element, style: { opacity: 0 } };
  };
  const App = () => {
    const fadeInH1 = useFadeIn(3);
    const fadeInP = useFadeIn(1, 3);
    return (
      <div className="App">
        <h1 {...fadeInH1}>Hi</h1>
        <p {...fadeInP}>Lorem</p>
  ```
