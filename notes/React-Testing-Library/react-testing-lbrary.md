# React Testing Library

## What is React Testing Library ?

React Testing Library is a UI-layer testing framework that helps us ensure that our React components are rendering and behaving properly.

The main advantages of RTL over other UI-layer testing frameworks are:

- It is built explicitly for testing React components.
- It allows us to test our components in a way that mimics real user interactions.

The logic behind this is that a user will not care about the implementation details of a React component, such as the component’s state, the props passed to it, etc. The user will only care about whether or not they are able to use the app. We should test our application with these same motivations.

## The Render and Screen Objects

Just like Jest, `create-react-app` includes React Testing Library by default

To start, we’ll take a look at two core objects, `render` and `screen`.

- `render()` is a function that we can use to virtually render components and make them available in our unit tests. Similar to `ReactDOM.render()`, RTL’s `render()` function takes in JSX as an argument.
- `screen` is a special object which can be thought of as a representation of the browser window. We can make sure that our virtually rendered components are available in the test by using the `screen.debug()` method, which prints out all the DOM contents.

The following code snippet shows the output of a unit test that prints out the DOM contents of a Greeting component 

```js
import { render, screen } from '@testing-library/react'

const Greeting = () => {
 return (<h1>Hello World</h1>)
};

it('prints out the contents of the DOM', () => {
   render(<Greeting />);
   screen.debug();
});

// Output:

<body>
 <div>
   <h1>
     Hello World
   </h1>
 </div>
</body>
```

After importing the `render` and `screen` values from `'@testing-library/react'`, a test is created using the `it()` function from the Jest testing framework. Inside, the `<Greeting>` component is virtually rendered, and then the resulting virtual DOM is printed via the `screen.debug()` method.

Notice how the output shows the rendered contents of `<Greeting>` (an `<h1>` element) and not the component itself. As was mentioned in the first exercise, React Testing Library strives to produce a testing environment that is as close to the user’s experience as possible.

Note: If we want to install React Testing Library locally , we can use the following command to install it as one of your project’s `devDependencies`:

`npm install --save-dev @testing-library/react`

## Querying with RTL

When we write tests for our applications, we often have to simulate user interaction with our application. This means we need to often work with the DOM elements to test how it reacts to user actions. To do so, we first have to query for and extract the DOM nodes from our virtually rendered components. Then, we can check and see if the extracted DOM nodes were rendered as expected. Fortunately for us, RTL has many built-in query methods that greatly simplify this process. In this part we will cover the `.getByX` query methods.

There are a number of `.getByX` query methods to choose from, and they are all accessible as methods on the `screen` object. Look at the example below, the `.getByText()` method is used to extract a DOM element with text that matches a specified string.

```js
import { render, screen } from '@testing-library/react';

const Button = () => {
    return <button type="submit" disabled>Submit</button>
};

it('Rendered a "Submit" button', () => {
  // Render the Button component
  render(<Button/>);
  // Extract the <button>Submit</button> node
  const button = screen.getByText('Submit'); 
});

```

If there is a match, meaning that it was able to find a matching node for the query, it will return the element. Otherwise, it will throw an error!

Similarly, another method is `.getByRole()`, which allows us to extract a DOM node by its role type. Look at the example below; it shows us another way we can query for the `<button>` element using `.getByRole()`.

```js
import { render } from '@testing-library/react';

const Button = () => {
    return <button type="submit" disabled>Submit</button>
};

it('extracts the button DOM node', () => {
  // Render the Button component
  render(<Button/>);
  // Extract the <button>Submit</button> node
  const button = screen.getByRole('button'); 
});

```

Now that we know how to query DOM nodes, we can test them using [Jest assertions](https://jestjs.io/docs/expect).  `expect.toBeChecked()` isn’t part of the regular set of Jest matchers, but instead is an extension provided by the `testing-library/jest-dom` library.

Just as before, if you’ve initialized your application with `create-react-app` as we have, `jest-dom` is included as a default dependency. Without configuration, we can use it out of the box.

The entire library can then be imported into our test file like so:

```js
import '@testing-library/jest-dom';
```

Here is an example of the `expect.toBeDisabled()` matcher being used to test a DOM node extracted with the `screen.getByRole()` method.

```js
import { render } from '@testing-library/react';
import '@testing-library/jest-dom';

const Button = () => {
    return <button type="submit" disabled>Submit</button>
};

it('shows the button as disabled', () => {
  // render Button component
  render(<Button/>);
  // Extract <button>Submit</button> Node
  const button = screen.getByRole('button');
  // Assert button is disabled
  expect(button).toBeDisabled();
});

```

> Note: If you want to install `jest-dom` locally and you did not use `create-react-app` to initialize your project, you can use the following command to install it as one of your project’s `devDependencies`:

```bash
npm install --save-dev @testing-library/jest-dom
```
