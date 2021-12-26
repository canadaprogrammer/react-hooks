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
  export default function App() {
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
  }

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
