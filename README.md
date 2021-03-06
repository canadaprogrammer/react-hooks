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

## `useNetwork()`

- ```jsx
  const useNetwork = (onChange) => {
    const [status, setStatus] = useState(navigator.onLine);
    const handleChange = () => {
      if (typeof onChange === "function") {
        onChange(navigator.onLine);
      }
      setStatus(navigator.onLine);
    };
    useEffect(() => {
      window.addEventListener("online", handleChange);
      window.addEventListener("offline", handleChange);
      return () => {
        window.removeEventListener("online", handleChange);
        window.removeEventListener("offline", handleChange);
      };
    }, []);
    return status;
  };
  const App = () => {
    const handleNetworkChange = (online) => {
      console.log(online ? "We just went online." : "we are offline.");
    };
    const onLine = useNetwork(handleNetworkChange);
    return (
      <div className="App">
        <h1>{onLine ? "Online" : "Offline"}</h1>
  ```

## `useScroll()`

- ```jsx
  const useScroll = () => {
    const [state, setState] = useState({
      x: 0,
      y: 0
    });
    const onScroll = () => {
      setState({ x: window.scrollX, y: window.scrollY });
    };
    useEffect(() => {
      window.addEventListener("scroll", onScroll);
      return () => {
        window.removeEventListener("scroll", onScroll);
      };
    });
    return state;
  };
  const App = () => {
    const { y } = useScroll();
    return (
      <div className="App" style={{ height: "1000vh" }}>
        <h1 style={{ position: "fixed", color: y > 100 ? "red" : "blue" }}>Hi</h1>
      </div>
  ```

## `useFullscreen()`

- ```jsx
  import aurora from "./aurora-g03221ec68_1920.jpg";

  const useFullscreen = (callback) => {
    const element = useRef();
    const runCallback = (isFull) => {
      if (callback && typeof callback === "function") {
        callback(isFull);
      }
    };
    const triggerFull = () => {
      if (element.current) {
        if (element.current.requestFullscreen) {
          element.current.requestFullscreen();
        } else if (element.current.mozRequestFullScreen) {
          element.current.mozRequestFullScreen();
        } else if (element.current.webkitRequestFullscreen) {
          element.current.webkitRequestFullscreen();
        } else if (element.current.msRequestFullscreen) {
          element.current.msRequestFullscreen();
        }
      }
      runCallback(true);
    };
    const exitFull = () => {
      if (document.exitFullscreen) {
        document.exitFullscreen();
      } else if (document.mozCancelFullScreen) {
        document.mozCancelFullScreen();
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen();
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen();
      }
      runCallback(false);
    };
    return { element, triggerFull, exitFull };
  };
  const App = () => {
    const onFullScr = (isFull) => {
      console.log(isFull ? "We're full!" : "We're small");
    };
    const { element, triggerFull, exitFull } = useFullscreen(onFullScr);
    return (
      <div className="App">
        <div ref={element}>
          <img
            src={aurora}
            alt=""
            style={{ width: "300px", display: "block", margin: "0 auto" }}
          />
          <button onClick={exitFull}>Exit fullscreen</button>
        </div>
        <button onClick={triggerFull}>Make fullscreen</button>
      </div>
  ```

## `useNotification()`

- Use Web API - `Notification()`

- ```jsx
  const useNotification = (title, options) => {
    if (!("Notification" in window)) {
      console.log("4");
      return;
    }
    const fireNotif = () => {
      if (Notification.permission !== "granted") {
        Notification.requestPermission().then((permission) => {
          if (permission === "granted") {
            console.log("1");
            new Notification(title, options);
          } else {
            console.log("2");
            return;
          }
        });
      } else {
        console.log("3");
        new Notification(title, options);
      }
    };
    return fireNotif;
  };
  const App = () => {
    const triggerNotif = useNotification("title", {
      body: "body"
    });
    return (
      <div className="App">
        <button onClick={triggerNotif}>Hello</button>
      </div>
  ```

## `useAxios()`

- ```jsx
  import defaultAxios from "axios";

  const useAxios = (options, axiosInstance = defaultAxios) => {
    const [state, setState] = useState({
      loading: true,
      error: null,
      data: null
    });
    // refetch
    const [trigger, setTrigger] = useState(0);
    const refetch = () => {
      setState({
        ...state,
        loading: true
      });
      setTrigger(Date.now());
    };
    useEffect(() => {
      if (!options.url) {
        return;
      }
      axiosInstance(options)
        .then((data) => {
          setState({
            ...state,
            loading: false,
            data
          });
        })
        .catch((error) => {
          setState({
            ...state,
            loading: false,
            error: error.message
          });
        });
    }, [trigger]);
    return { ...state, refetch };
  };

  const App = () => {
    const { loading, data, error, refetch } = useAxios({
      url: "yts.am/api/v2/list_movies.json"
    });
    console.log(
      `loading: ${loading}\nerror: ${error}\ndata: ${JSON.stringify(data)}`
    );
    return (
      <div className="App">
        <h1>{data && data.status}</h1>
        <h2>{error}</h2>
        <h2>{loading && "Loading"}</h2>
        <button onClick={refetch}>Refetch</button>
      </div>
  ```

## Publishing Package to NPM

- Example: useTitle

- Created a `useTitle` folder, and put `index.js`, `useTitle.js`, and `README.md`

  - `index.js`

    - `export { useTitle as default } from './useTitle';`

  - `useTitle.js`

    - ```jsx
      import { useState, useEffect } from 'react';

      export const useTitle = (initialTitle) => {
        const [title, setTitle] = useState(initialTitle);
        const updateTitle = () => {
          const htmlTitle = document.querySelector('title');
          htmlTitle.innerText = title;
        };
        useEffect(updateTitle, [title]);
        return setTitle;
      };
      ```

  - `README.md`

    > # @testing-hooks/use-title
    >
    > React Hook to update your document's title
    >
    > ## Installation
    >
    > `npm i @testing-hooks/use-title` > `yarn add @testing-hooks/use-title`
    >
    > ## Usage
    >
    > ```jsx
    > import React from 'react';
    > import useTitle from '@testing-hooks/use-title';
    > const App = () => {
    >   useTitle('Test Hooks');
    >   return <h1>Hello</h1>;
    > };
    > ```
    >
    > ## Arguments
    >
    > | Argument | Type   | Description                        | Required |
    > | -------- | ------ | ---------------------------------- | -------- |
    > | title    | string | The title on title tag of head tag | yes      |

- On the folder

  - `npm init`

    - package name: @testing-hooks/use-title

    - description: React Hook to update your document's title

  - `npm i react react-dom`

  - On `package.json`

    - Remove scripts from `package.json`

    - Change `dependencies` to `peerDependencies` for not duplication

  - Package name will start @

  - On npmjs.com

    - Create New Organization

      - Name: jin-hooks

  - `npm login`

  - `npm publish --access public`

- Test on `codesandbox.io`

  - Add dependency, `@testing-hooks/use-title`
