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

## Different Query Methods

 RTL has another category of query methods called `.queryByX`.

Look at the code below. It shows the code for a simple component that renders a header with the text `'Hello World!'` and then changes the text to `'Goodbye!'` 500ms after the user clicks a button. We will be using this `App` component to demonstrate the different query types.

```js
import { useState } from 'react';

const App = () => {

  const [text, setText] = useState('Hello World!');

  // Changes header text after interval of 500ms
  const handleClick = () => {
    setTimeout(() => {
        setText('Goodbye!');
    }, 500);
  };

  return (
    <div>
      <h1>{text}</h1>
      <button onClick={handleClick}>click me</button>
    </div>
  )
};

export default App;
```

Let’s look at [`.queryByX`](https://testing-library.com/docs/queries/about/) variant. The `.queryByX` methods return `null` if they don’t find a DOM node, unlike the `.getByX` methods, which throw an error and immediately cause the test to fail. This is useful when asserting that an element is NOT present in the DOM.

In this example, we want to confirm that the `header` does not (yet) contain the text `'Goodbye'`:

```js
import App from './components/App';
import { render, screen } from '@testing-library/react';

it('Header should not show Goodbye yet', () => {
  // Render App
  render(<App />);
  // Attempt to extract the header element
  const header = screen.queryByText('Goodbye!');
  // Assert null as we have not clicked the button
  expect(header).toBeNull();
});
```

By using the `.queryByText()`, variant when there is no element with the text `'Goodbye!'`, the value `null` is returned, and we can successfully validate this with `expect(header).toBeNull()`. If the `.getByText()` method were used instead, the test would fail immediately due to the error rather than continuing on to the `expect()` assertion.

## Querying Asynchronous Components

Now, let’s discuss querying for asynchronous elements. Imagine we’re testing an application comprising components that perform asynchronous operations, such as fetching data from an API, triggering animations, or executing delayed rendering. How would we write tests that will wait for these elements to appear?

We can use [`.findByX`](https://testing-library.com/docs/queries/about/) methods. The `.findByX` methods are used to query for asynchronous elements, which will eventually appear in the DOM. The `.findByX` methods work by returning a Promise, which resolves when the queried element renders in the DOM. As such, the `async`/`await` keywords can be used to enable asynchronous logic.

Let’s look back at the program again. Recall that the header has a 500ms delay. We want to confirm that the `header` will display the text `'Goodbye'` after the button is clicked.

```js
// Changes header text after interval of 500ms
const handleClick = () => {
  setTimeout(() => {
      setText('Goodbye!');
  }, 500);
};
```

We can write our test like this. This example uses the `userEvent` library, which will be covered in depth in the next exercise, to simulate clicking on the button.

import App from './components/App';
import { render, screen } from '@testing-library/react';

```js
it('should show text content as Goodbye', async () => {
  // Render App
  render(<App />);
  // Extract button node 
  const button = screen.getByRole('button');
  // click button
  userEvent.click(button);
  // Wait for the text 'Goodbye!' to appear
  const header = await screen.findByText('Goodbye!');
  // Assert header to exist in the DOM
  expect(header).toBeInTheDocument();
});
```

In the example above, we use `.findByText()` since the `'Goodbye!'` message does not render immediately. This is because our `handleClick()` function changes the text after an interval of 500ms. So, we have to wait a bit before the new text is rendered in the DOM.

Observe the `async` and `await` keywords in the example above. Remember that `findBy` methods return a Promise, and thus, the callback function that carries out the unit test must be identified as `async` while the `screen.findByText()` method must be preceded by `await`.

## Mimicking User Interactions

So far, we’ve learned how to query and extract the different DOM nodes from our React components. Now, it’s time for us to learn how to mimic user interactions, such as clicking a checkbox and typing text. Once again, this entire process has been made easier for us with the help of another library in the `@testing-library` suite: `@testing-library/user-event`.

This library exports a single object, `userEvent`, that can be imported in a test file like so:

```js
import userEvent from '@testing-library/user-event';
```

The `userEvent` object contains many built-in methods that allow us to mimic user interactions. Typically, they follow the same syntax pattern:

```javascript
userEvent.interactionType(nodeToInteractWith);
```

Here is an example where we mimic a user filling in a text box. Note that in this case, a second argument is provided as the text to be typed into the box.

```javascript
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import '@testing-library/jest-dom';

const GreetingForm = () => {
  return(
    <form>
      <label htmlFor="greeting">
        Greeting:
      </label>
      <input type="text" id="greeting" />
      <input type="submit" value="Submit" />
    </form>
  );
};

it('should show text content as Hey Mack!', () => {
  // Render the component to test
  render(<GreetingForm />);
  // Extract the textbox component
  const textbox = screen.getByRole('textbox');
  // Simulate typing 'Hey Mack!'
  userEvent.type(textbox, 'Hey Mack!');
  // Assert textbox has text content 'Hey Mack!'
  expect(textbox).toHaveValue('Hey Mack!');
});
```

In the example above, the `userEvent.type()` method is used, which accepts a DOM node to interact with (`textbox`) and a string to type into that node (`’Hey Mack!’).

Note: If you want to install `user-event` locally and you did not use `create-react-app` to initialize your project, you can use the following command to install it as one of your project’s `devDependencies`:

```bash
npm install --save-dev @testing-library/user-event
```

## The waitFor() method

In the previous exercise, we learned about the `.findByX` query methods that allow us to test components that render asynchronously. But what about components that disappear asynchronously?

Look at the example below. We have a component that displays a header. This header is removed after 250 ms when the button “Remove Header” is clicked.

```js
// In header.js:
export const Header = () => {
 const handleClick = () => {
   setTimeout(() => {
     document.querySelector('h1').remove()
   }, 250);
 };
 return (
   <div>
     <h1>Hey Everybody</h1>
     <button onClick = {handleClick}>Remove Header</button>
   </div>
 );
};
```

How would you test that the header is removed? Using `screen.findByX()` methods won’t work because there won’t be an element to query once it’s removed! Using only `screen.queryByX()` methods won’t work either, as the component is removed asynchronously.

Fortunately, RTL provides another function that can be used for asynchronous testing that will be perfect for this scenario - the `waitFor()` function. In order to use this function, we need to import it from `@testing-library/react`.

```js
import { waitFor } from '@testing-library/react';
```

As with the other async functions, the `waitFor()` function returns a Promise, so we have to preface its call with the `await` keyword. It takes a callback function as an argument where we can make asynchronous function calls, perform queries, and/or run assertions.

```js
await waitFor(() => {
  expect(someAsyncMethod).toHaveBeenCalled();
  const someAsyncNode = screen.getByText('hello world');
  expect(someAsyncNode).toBeInTheDocument();
});
```

Now, let’s get back to the example. To test that a component disappears asynchronously, we can combine the `waitFor()` function with `.queryByX()` methods:

```js
import { waitFor, render, screen } from '@testing-library/react';
import '@testing-library/jest-dom';
import userEvent from '@testing-library/user-event';
import { Header } from './heaader.js'

it('should remove header display', async () => {
  // Render Header
  render(<Header/>)
  // Extract button node 
  const button = screen.getByRole('button');
  // click button
  userEvent.click(button);

  // Wait for the element to be removed asynchronously
  await waitFor(() => {
    const header = screen.queryByText('Hey Everybody');
    expect(header).toBeNull()
  })
});
```

In our unit test, the header will be removed 250ms after the button has been clicked. The callback function inside `waitFor()` confirms this by querying for this element and then waiting for the `expect()` assertion to pass.

The `waitFor()` method can also optionally accept an `options` object as a second argument. This object can be used to control how long to wait for before aborting and much more. Though the details of this `options` object are beyond the scope of the lesson, you can read more about it in the [docs](https://testing-library.com/docs/dom-testing-library/api-async/#waitfor).

## Testing for Accessibility

Now that we’ve covered a wide stretch of RTL methods, let’s talk about accessibility and how it can help you write better tests. One of the guiding principles of React Testing Library is to write “queries that reflect the experience of visual/mouse users as well as those that use assistive technology.” Usually, this means using the same text that the user would see, rather than the implementation details like class names or IDs.

Writing tests that adhere to this principle forces you to make your applications more accessible. If a test can find and interact with your elements by their text, it’s more likely that a user who uses assistive technology can as well.

One way we can write tests with accessibility concerns in mind is by sticking to querying with `ByRole` queries (`getByRole`, `findByRole`, `queryByRole`). The `ByRole` variant will be able to query any elements within the [accessibility tree](https://developer.mozilla.org/en-US/docs/Glossary/Accessibility_tree). If you are unable to query for the component you want to test, you may have just exposed a part of your application that is inaccessible.

Let’s see this in action. Suppose we’re testing an input form:

```js
<input id="search" value="" />
```

If we try to use `getByRole`, it will not be able to query for this element. This exposes a component that is inaccessible. To fix it, we would have to make some modifications to the element by adding a `type` which provides a [role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) and `label` with an `htmlFor` attribute which provides an [accessible name](https://www.tpgi.com/what-is-an-accessible-name/) for an element. Note that how you assign accessible names differs based on the tag you’re using.

```js
<label htmlFor="search">
   <input type="search" id="search" value="" />
</label>
```

Then our query can be:

```js
screen.getByRole('searchbox', {name: /search/i})
```

Great! We’ve knocked out two birds with one stone here. We made our input more accessible, and we were able to narrow down what query to use.

While `ByRole` is a great default for accessibility, you can visit the [React Testing Library Playground](https://testing-playground.com/) for suggestions on accessible queries for more complex needs.

Including accessibility considerations in your testing process can help proactively identify and address potential accessibility issues. This not only ensures that your application is inclusive and usable by a wider range of users but also enhances the overall quality and user experience.

> 
