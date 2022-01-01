# @testing-hooks/use-title

- React Hook to update your document's title

## Installation

- `npm i @testing-hooks/use-title`

- `yarn add @testing-hooks/use-title`

## Usage

- ```jsx
  import React from 'react';
  import useTitle from '@testing-hooks/use-title';

  const App = () => {
    useTitle('Test Hooks');
    return <h1>Hello</h1>;
  };
  ```

## Arguments

- | Argument | Type   | Description                        | Required |
  | -------- | ------ | ---------------------------------- | -------- |
  | title    | string | The title on title tag of head tag | yes      |
