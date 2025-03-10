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
