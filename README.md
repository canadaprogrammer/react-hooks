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
