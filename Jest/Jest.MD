# Jest

#### What is Jest ?

Jest provides the two key ingredients needed for testing:

1. An assertion library: an API of functions for validating a program’s functionality
2. A test runner: a tool that executes tests and provides outputted test summaries



## Getting Started With Jest

Jest is the default test runner included and preconfigured in `create-react-app`. There is no further installation necessary when using `create-react-app` to build a new app!

To begin testing, we need to make sure that our test files are located, or match the following name conventions under the **/src** top-level directory:

- files with names ending in **.test.js**
- files with names ending in **.spec.js**
- **.js** files within a **__tests__/** directory

How we store test files in your project may depend on our specific needs. We may choose to colocate test files with the code being tested or to separate them into a **__tests__** directory.

File Structure usually recommended 

![](../assets/test-file-structure.png)

It is a good practice to match the name of the test file to the file that we want to test. For example, if we were to test a file called **math.js**, we might create a file called **math.test.js**.

Once we have our test files created , we can run the test using the npm command:

`npm test`

This will launch the test in *watch mode*, allowing the test to re-run every time the file is saved! We will need to type `q` in the terminal to quit out of the watch mode.
