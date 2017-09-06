Tests
=====
The [Augur](https://github.com/AugurProject/augur) repository, and the middleware [augur.js](https://github.com/AugurProject/augur.js) repository both contain tests. Tests are run using [Mocha](http://mochajs.org/). Mocha does not need to be installed globally to run the test suites, but if you would like to run specific tests explicitly then it is helpful to globally install Mocha.

To globally install Mocha using `npm` you would run the following command:

`$ npm install -g mocha`

To globally install Mocha using `yarn`, you would run the following command:

`$ yarn global add mocha`

Once you have Mocha installed, you can run a specific test case like so:

`$ mocha ./test/unit/core/cash.js`

Augur and Augur.js both contain their own sets of unit tests and Augur.js has a an integration test suite as well. The following sections will provide more details about the differences between the two.

Running Tests In Augur
----------------------
[Augur](https://github.com/AugurProject/augur) has a suite of unit tests that can be run using `npm` or `yarn`. In order to run tests, makes sure you have installed the dependencies for Augur first by using the following command:

`$ npm install`

or if you are using `yarn`:

`$ yarn`

Once you have installed the dependencies for Augur, you can run the following command using `npm` to run the unit tests:

`$ npm test`

of if you prefer to use `yarn`, the command becomes:

`$ yarn test`

The Augur repository is also following a set of standard coding rules. In order to check if your code is following the standards, you can run a linting command. To lint the Augur repository using `npm`, you would run the following command:

`$ npm run lint`

and to issue the same command with `yarn`, you would enter this:

`$ yarn lint`

<aside class="notice">All pull requests made to the Augur project must be properly linted first and any new functionality added must include tests or updated tests. The Augur Team will not accept a pull request that doesn't include updated tests or breaks the linting standards we have for the Augur codebase.</aside>

Running Tests In Augur.js
-------------------------
[augur.js](https://github.com/AugurProject/augur.js) includes both Unit tests and integration tests that can be run with `npm` or `yarn`. In order to run tests, makes sure you have installed the dependencies for Augur.js first by using the following command:

`$ npm install`

or if you are using `yarn`:

`$ yarn`

Once you have installed the dependencies for Augur.js, you can run the following command using `npm` to run the unit tests:

`$ npm run test`

To run integration tests with `npm`, use the following command:

`$ npm run integration-test`

If you would prefer to use `yarn` the command to run unit tests becomes:

`$ yarn test`

to run integration tests, the command is:

`$ yarn integration-test`

Augur.js also follows a standard for it's code and also has a linting command. The command using `npm` is:

`$ npm run lint`

And if you prefer `yarn` then the command is:

`$ yarn lint`

<aside class="notice">All pull requests made to the Augur.js project must be properly linted first and any new functionality added must include tests or updated tests. The Augur Team will not accept a pull request that doesn't include updated tests or breaks the linting standards we have for the Augur.js codebase.</aside>
