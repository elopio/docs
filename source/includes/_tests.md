Tests
=====

[augur.js](https://github.com/AugurProject/augur.js) includes unit tests.  Tests are run using [Mocha](http://mochajs.org/). Mocha does not need to be installed globally to run the test suites, but if you would like to run specific tests explicitly then it is helpful to globally install Mocha. To install Mocha globally:

`$ npm install -g mocha`

To run a specific test, like our markets unit tests for example, you simply add the path to the mocha command like so:

`$ mocha ./test/unit/markets/markets.js`

There are two test suites -- unit tests and integration tests. Generally, it is recommended to only run the integration tests on a private chain.

To run the unit tests:

`$ npm run test`

To run the integration tests:

`$ npm run integration-test`
