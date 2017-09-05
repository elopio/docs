---
title: Augur documentation

language_tabs:
  - javascript: Code

toc_footers:
  - <a href="https://augur.net">Augur</a>
  - <a href="https://app.augur.net">App</a>
  - <a href="https://github.com/AugurProject">Github</a>
  - <a href="https://twitter.com/AugurProject">Twitter</a>

includes:
  - architecture
  - trading
  - reporting
  - generalAPI
  - callAPI
  - transactionAPI
  - events
  - numbers
  - accounts
  - rpc
  - tests
  - errors
  - uiConventions
  - contributing
  - glossary

search: true
---
Overview
========
<aside class="notice"><b>Please Note:</b> These docs are currently being updated as we approach the launch of Augur. The Augur Team plans to have these docs fully updated prior to launching Augur. Please bare with us as we update this document and be advised that some of this information is out of date or may be changed prior to launch. Thank you for your patience.</aside>

[Augur](https://app.augur.net) is a decentralized prediction market platform that runs on Ethereum.

The stable Augur application is built and hosted at [https://app.augur.net](https://app.augur.net). If you want to use or help test Augur, you do not need to download or install anything! Just go to [https://app.augur.net](https://app.augur.net) and start using it. (If you want to use our cutting-edge development client, this is maintained at [https://augur-dev.firebaseapp.com](https://augur-dev.firebaseapp.com) -- warning, we push changes to augur-dev.firebaseapp.com pretty rapidly, so it can be a bit buggy! We suggest simply using [https://app.augur.net](https://app.augur.net) for a more stable experience.)

Augur has it's own dedicated stack exchange which can be found at [augur.stackexchange.com](https://augur.stackexchange.com/). The stack exchange allows people to ask questions about Augur and get answers to those questions. It's a great resource to find the answers for questions you might have that aren't answered directly in these documents. You can also try our Slack, which is open to the pubic. If you'd like to join the conversation, just go to [slack.augur.net](http://slack.augur.net) and sign up. Generally, questions are based asked in the `#dev` or `#general` channels.

Getting Started
---------------

If you want to help develop Augur, you will need to build the client from source. To do this, first install [Node.js](https://nodejs.org/), and [git](https://git-scm.com/downloads).

First we need to clone the Augur repository from [Github](https://www.github.com/AugurProject/augur), open up your terminal and enter the following command to clone the repository:

`$ git clone https://www.github.com/AugurProject/augur.git`

Now navigate into the newly created `augur` folder by running the following command:

`$ cd augur`

Now we need to install Augur's dependencies. This can be done with `npm` or `yarn`. Both methods are detailed in the next sections.

Using NPM
---------
[Node.js](https://nodejs.org/) comes with a built in package manager called `npm`. `npm` is used to install dependencies for Augur, build the Augur project from source code, run [tests](http://docs.augur.net/#tests), and start a web server to host Augur among other things. To install Augur's dependencies using `npm` we will run the following command:

`$ npm install`

To build Augur from the source code, we can run the following command:

`$ npm run build`

After we build Augur from the source files, we are ready to fire up a local web sever. To do this, we run the following command:

`$ npm start`

 If you would like to enable hot-reloading, which automatically updates your changes on the localhost, then instead of running `npm start`, we would instead enter:

`$ npm run dev`

This is a useful way to do some hacking on the Augur source code and see your changes reflected in real time.

Now go to [http://localhost:8080](http://localhost:8080) in your web browser. You should be greeted by Augur's most popular topics page. Congratulations, you're ready to start hacking!

Using Yarn
----------
Some people prefer to use [yarn](https://yarnpkg.com/en/) instead of `npm`. If you prefer to use `yarn` then you would run the following command to install the Augur dependencies:

`$ yarn`

To build Augur from the source code:

`$ yarn build`

After building, you can run the following command to start the web server:

`$ yarn start`

If you would like hot-reloading for development purposes then, instead of `yarn start`, the `yarn` command is:

`$ yarn dev`

Finally, go to [http://localhost:8080](http://localhost:8080) in your web browser. You should be greeted by Augur's most popular topics page. Congratulations, you're ready to start hacking!

If you plan to submit a Pull Request to the Augur Project, make sure to read through the [contributing](http://docs.augur.net/#contributing) section of these documents before you submit the request.

<aside class="notice"><b>If you are using or helping develop the Augur client (i.e., the front-end/user interface), it is usually not necessary for you to run a local Ethereum node on your computer.</b> We're already running several <a href="http://docs.augur.net/#hosted-node">hosted nodes</a> that you can simply plug into! If you are running an Ethereum node, the client will automatically detect it and begin talking to it; if you are not, the client will default to using the hosted nodes.</aside>
