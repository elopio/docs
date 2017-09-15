Glossary
========
<aside class="notice">The Glossary section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>
<!--
this section will include subsections for all terms used and descriptions of what they are and what they do. This is to avoid repeating them same information over and over again in function descriptions in the API section. This section should be easy to navigate and easy to link to specific concepts. To that end, each concept should have it's own subsection.

Goals:
  - Easy to navigate
    - every term has it's own "section" for easy linking
    - alphabetical
  - Human Readable language
 -->
This section of the documentation is dedicated to terms found and used throughout the rest of documentation. Below you will find sections about terms used in Augur. The goal is to explain everything that might be confusing in an easy to understand way.

## All Reporting

All Reporting is the third level of the [Reporting](#report) system, second if the [Market](#market) never had a set [Automated Reporter](#automated-reporter). If a Market has gone through [Limited Reporting](#limited-reporting) and the [Market Resolution](#market-resolution) has been challenged then the Market will be moved into the upcoming All Reporting level of the next [Reporting Cycle](#reporting-cycle). Every Reporter is expected to report on All Reporting Markets during a [Reporting Window](#reporting-window) in which they are Reporting. All Reporting lasts for 27 days and is followed by a 3 day [Dispute Phase](#dispute-phase) where a [Dispute Bond](#dispute-bond) can be posted to force the final reporting system level, a [Fork](#fork).

## Ask Order

An Ask Order is an [Order](#order) indicating the desire of the [Maker](#maker) to sell [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of a [Bid Order](#bid-order).

## Automated Reporter

An Automated Reporter is a single address designated to [Resolve](#market-resolution) a [Market](#market) during [Automated Reporting](#automated-reporting). The Automated Reporter is set by the [Market Creator](#market-creator) during Market Creation. If no Automated Reporter is set then the market will use [Limited Reporting](#limited-reporting) as it's first attempt to be resolved, instead of Automated Reporting.

## Automated Reporting

Automated Reporting is the first and fastest way that a [Market](#market) can be [Reported](#report) on and [Resolved](#market-resolution). One address will be responsible for resolving the Market and will have 3 days to do so after a Market's [End Time](#end-time). After the Automated Report has been submitted by the automated [Reporter](#reporter) then a 3 day [Dispute Phase](#dispute-phase) begins where in anyone can post a bond to dispute the resolved [Outcome](#outcome) of the market. If the [Dispute Bond](#dispute-bond) is posted then the market is moved into the next [Reporting Cycle](#reporting-cycle) and will be subject to [Limited Reporting](#limited-reporting).

## Bid Order

A Bid Order is an [Order](#order) indicating the desire of the [Maker](#maker) to buy [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of an [Ask Order](#ask-order).

## Binary Market

A Binary Market is a [market](#market) with only two [outcomes](#outcome), as well as Indeterminate which is always a possible outcome. Binary markets are for yes or no questions, if you need more than a yes or no then a [Categorical](#categorical-market) or [Scalar](#scalar-market) market might fit your needs better.

## Branch

A Branch can be thought of as a separate universe of Augur where the [Winning Outcome](#winning-outcome) [Resolved](#market-resolution) for a [All Reporting](#all-reporting) [Market](#market) is [Disputed](#dispute-bond) and multiple versions of Augur are needed to resolve the dispute. Each version of Augur is a different Branch, where each version will feature a copy of Augur's state with the one difference being the Outcome of the Market that caused the [Fork](#fork).

## Categorical Market

A Categorical Market is a [market](#market) with more than 2 potential [outcomes](#outcome), but no more than 8. As with all markets, Indeterminate is also an outcome not included in the 8 outcome maximum. Categorical Markets are best for multiple choice type questions, which team wins a tournament or what color tie the President of the United States wears at his next press conference. If you just need a yes or no question, you will probably want to make a [Binary Market](#binary-market). If you wanted to guess the temperature in degrees on a certain day, you would probably want to use a [Scalar Market](#scalar-market) for that, as it would be very difficult to pin down 8 possibilities and it's not a yes or no question.

## Challenge

Challenge is used to describe the act of a [REP](#rep) holder posting a [Dispute Bond](#dispute-bond) to dispute or "challenge" the [Resolved](#market-resolution) [Winning Outcome](#winning-outcome) of a [Market](#market) before it is [Finalized](#finalized-market).

## Complete Set

A Complete Set is a collection of [Shares](#shares) in every [Outcome](#outcome). Complete Sets are created when the [Maker](#maker) and [Taker](#taker) of an [Order](#order) both use currency to pay for the trade, as opposed to one or both parties using Shares to complete the trade. When both parties use shares to complete the trade then a Complete Set will be formed and settled (destroyed). The range of the [market](#market), ([maxDisplayPrice](#maximum-display-price) - [minDisplayPrice](#minimum-display-price)) - [Trading Fees](#trading-fees) ETH will then be paid out proportionally to both the Maker and Taker based on their respective Shares value at the time of settlement. The Trading Fees extracted will go toward paying for the reporting system and paying the [Market Creator](#market-creator) their set [Trading Fee](#trading-fee) from share settlement.

## Dispute Bond

A Dispute Bond is a bond posted to force another round of [Reporting](#report) if a [Reporter](#reporter) feels the [Outcome](#outcome) of a [Market](#market) hasn't been [Resolved](#market-resolution) accurately. If the market's outcome is changed in the forced round of reporting then the poster of the dispute bond will get their money back for successfully challenging the false outcome of the market.

## Dispute Phase

A Dispute Phase is a three (3) day window after a [Market](#market) has been [Resolved](#market-resolution) ([Reported](#report) on) before which the [Outcome](#outcome) of the market resolution is finalized. During this 3 day period, a [Reporter](#reporter) can post a [Dispute Bond](#dispute-bond) for a particular market if they would like to force another round of reporting on that market. The market will be moved into the next coming [Reporting Window](#reporting-window).

## End Time

End Time is the date and time that a [market](#market)'s event will have come to pass and should be known. After this date and time has passed the market will get reported on and finalized.

## Fill Order

Filling an [Order](#order) is when a [Taker](#taker) provides what the [Maker](#maker) of the order is seeking in their order. If a taker only provides some of what the Maker wants then it's known as a partial fill. If the Taker provides exactly what the Maker requests then it's known as completely filling the order.

## Finalized Market

A Finalized Market is a [Market](#market) that has been [Resolved](#market-resolution) and has not been disputed, the [Winning Outcome](#winning-outcome) is now final. This market is officially closed and [Share](#shares) holders can [settle](#settlement) their shares with the market.

## Fork

A Fork occurs if a [All Reporting](#all-reporting) [Market Resolution](#market-resolution) is disputed. A Fork causes Augur to create multiple [Branches](#branch) of itself, where each branch is a copy of the current state of Augur with the only difference being the finalized [outcome](#outcome) of the [Market](#market) that caused the Fork to happen by being disputed after All Reporting. There will be a branch created for each possible outcome of the market, including invalid. The idea here is that people will want to trade in the branch of Augur that actually reflects reality (the correct outcome of the market). The branch that people have accepted as reality will have more trading volume on it and will be considered the main branch.

## Limited Reporting

Limited Reporting is the second level of [Reporting](#report) and is the first attempt at [Market Resolution](#market-resolution) if an [Automated Reporter](#automated-reporter) hasn't been set by the [Market Creator](#market-creator). Limited Reporting means the [Market](#market) needs to be reported on by a certain amount of [Reporters](#reporter) before the market is resolved. [Limited Reporting](#limited-reporting) lasts for twenty seven (27) days and has a three (3) day [Dispute Phase](#dispute-phase) following market resolution before the market is finalized.

## Maker

A Maker is the creator of an [Order](#order) that is placed on the [Order Book](#order-book). They escrow currency or [Shares](#shares) into their Order in order to buy or sell Shares of an [Outcome](#outcome) of a [Market](#market).

## Market

A market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each market created on the Augur network will have an automatically managed order book, which will allow users to buy and sell shares of different outcomes of the market. The [Market Creator](#market-creator) can set the [Trading Fee](#trading-fee) for the market, which once set cannot be lowered, which will determine their cut of all shares settled on their market. There are three different market types supported by Augur, they are: [Binary](#binary-market), [Categorical](#categorical-market), and [Scalar](#scalar-market).

## Market Creator

A Market Creator is simply a user who created a [market](#market). They are charged a small fee to make a new market but can determine the [Trading Fee](#trading-fee) for settlement of [Shares](#shares) on that market. Market Creators are incentivized to create popular markets so as to generate the most amount of settlement fees for themselves. Other information a market requires is the actual question being purposed, the type of market, the number of [Outcomes](#outcome), [End Time](#end-time), and a [Topic](#topic).

## Market Resolution

Market Resolution occurs when a [Market](#market) has been [Reported](#report) on and has a [Winning Outcome](#winning-outcome). Market's are resolved for a period of 3 days, in which [Reporters](#reporters) are allowed to post a [Dispute Bond](#dispute-bond) to force another round of reporting for the Market in question. If a market's resolution is not challenged for 3 days it becomes a [Finalized Market](#finalized-market).

## Maximum Display Price

The Maximum Display Price (often seen as `maxDisplayPrice`) is the maximum price allowed for a share on a [market](#market). For [Binary](#binary-market) or [Categorical](#categorical-market) Markets this value is always 1, as in 1 ETH. [Scalar](#scalar-market) markets' Maximum Display Price would be the top end of the range set by the [Market Creator](#market-creator).

## Minimum Display Price

The Minimum Display Price (often seen as `minDisplayPrice`) is the minimum price allowed for a share on a [market](#market). For [Binary](#binary-market) or [Categorical](#categorical-market) Markets this value is always 0, as in 0 ETH. [Scalar](#scalar-market) markets' Minimum Display Price would be the bottom end of the range set by the [Market Creator](#market-creator).

## Open Order

An Open Order is an [Order](#order) that is currently on the [Order Book](#order-book) and has not been completely [Filled](#fill-order).

## Order

An Order can be thought of as the recorded interest of a user to execute a trade of some amount of [Shares](#shares) at a defined price point. Orders come in two types, [Bid Orders](#bid-order) and [Ask Orders](#ask-order), which indicate an attempt to buy or sell respectively. The [Maker](#maker) of the order will also need to escrow currency or shares in order to provide their half of the trade. The information stored in an Order is as follows: the type of order, the [Market](#market) the order is trading on, the [Outcome](#outcome) the order is concerned with buying or selling, the Maker's address, the price per share, the amount of shares to trade, what block number the order was created during, the amount of currency or Shares escrowed in the order by the Maker for their half of the trade.

## Order Book

The Order Book is the collection of all [Open Orders](#open-order) currently available for a [Market](#market). [Orders](#order) are placed on the order book by [Makers](#maker) and are [Filled](#fill-order) by [Takers](#taker). Orders are divided up by which type, or side, of order they are [Bid](#bid-order) or [Ask](#ask-order). Orders are further divided up by [Outcome](#outcome).

## Outcome

An outcome is a potential result of a [Market](#market)'s future event. For example, a market with a question of "Will it rain anywhere in New York City on November 1st, 2032 as reported by www.weather.com?" would have three potential outcomes: Yes, No, and Indeterminate. Indeterminate would be an option if the world blew up before November 1st, 2032 and there was no New York City or www.weather.com to verify the market. More realistically this can happen for markets that have too vague of a question. A good example of a vague market that would most likely be voted indeterminate would be "Does God exist?" as no one has a definitive answer.

## Position

A Position is the amount of [Shares](#share) that is owned (a long position) or borrowed and then sold (a short position) by an individual. A position can be profitable or unprofitable, depending on [Market](#market) movements. Positions can be Open or Closed. An Open Position simply means you currently own the shares, where as a closed position means you have now redeemed your shares and have cashed out for currency. Closing a short positions means you are buying the Shares of the outcome you are short on, where as closing a long position means selling the Shares you own.

## Registration Token

A Registration Token is purchasable by [REP](#REP) holders for REP and is used as a sort of ticket to participate in the [Reporting Cycle](#reporting-cycle) the Registration Token belongs to. Once the Reporting Cycle has started, it will cost the Registration Token to be able to report. When the Registration Token is spent the REP used to pay for it is refunded. The Registration Token cannot be spent once the reporting cycle it belongs to has passed, and the deposited REP is not refunded. The Registration Token is designed as a sort of deposit to ensure that [Reporters](#reporters) who sign up to [Report](#report) in the coming Reporting Cycle actually show up and participate in the reporting process, or lose their deposit.

## REP

REP, also known as Reputation, Reputation Tokens, or REP Tokens, is the currency used by the Augur Decentralized Oracle System. REP is used to purchase a [Registration Token](#registration-token) for an upcoming [Reporting Window](#reporting-window) and to [Report](#report) on the [outcome](#outcome) of [Markets](#market). Once you have registered for a Reporting Window, when the time comes to report you will be shown markets that need to be finalized. You will be asked to wager REP on an outcome based on how confident you are in that outcome being the result of the question asked in the market. The markets you will see for reporting are all past their [End Time](#end-time) and the outcome should be determinable. If the outcome is not determinable you can wager your rep into the Indeterminate outcome. The more REP you wager, the larger the share of the [Reporting Fees](#reporting-fee) you will receive if you report with the consensus.

## Report

A Report, or Reporting, is the submission of [REP](#REP) wagered on the [Outcome](#outcome) of a [Closed Market](#closed-market) by a [Reporter](#reporter) in which the reporter has selected the accurate outcome of the [Market](#market). The wagering and submitting of the wager is the act of Reporting. Reporting as a term can be used to describe the act of submitting a report for a single market or a number of markets. Reporting takes place during a [Reporting Window](#reporting-window), sometimes called a [Reporting Cycle](#reporting-cycle) or [Reporting Period](#reporting-period). For a detailed breakdown of the Reporting System in Augur, check out the [Reporting Section](#reporting).

## Reporter

A Reporter, or Registered Reporter, is a [REP](#REP) holder who has registered for the current Reporting Cycle by purchasing a [Registration Token](#registration-token). The Reporter is expected to [Report](#report) on at least a minimum number of [Markets](#markets) and ideally should be [Reporting](#report) accurately as these market's descriptions should have a known outcome at this point in time.

## Reporting Cycle

The Reporting Cycle is a period of 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). This term is interchangeable with [Reporting Period](#reporting-period) and [Reporting Window](#reporting-window).

## Reporting Fee

The Reporting Fee is used to help pay for the decentralized oracle system. When shares are settled (aka destroyed), before paying out to the share holders Augur will extract the [Trading Fees](#trading-fees), which includes the [Trading Fee](#trading-fee) and The Reporting Fee. The Reporting Fees are sent to the Reporting Window that contains the [market](#market) being traded on, and are later used to pay [REP](#rep) holders for reporting on the outcome of markets.

## Reporting Period

The Reporting Period is 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). This term is interchangeable with [Reporting Cycle](#reporting-cycle) and [Reporting Window](#reporting-window).

## Reporting Phase

The Reporting Phase occurs in the first twenty seven (27) days of a [Reporting Window]. During this phase, [Limited Reporting](#limited-reporting) and [All Reporting](#all-reporting) [Markets](#market) are [Reported](#report) on by [Reporters](#reporter).

## Reporting Window

The Reporting Window is a period of 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). This term is interchangeable with [Reporting Period](#reporting-period) and [Reporting Cycle](#reporting-cycle).

## Scalar Market

A Scalar Market is a [Market](#market) with a range for potential [outcomes](#outcome). A scalar market example might be "According to www.weather.com, what will the temperature in Fahrenheit be at SFO on January 3rd, 2062 at 1:00pm?". In this example market, we might set the [minDisplayPrice](#minimum-display-price) of the market to -50 and the [maxDisplayPrice](#maximum-display-price) to 150. This would allow for the market to settle on any number between the two. Sometimes you don't need a range of potential outcomes, only a simple yes/no or a small number of choices, in these cases you would want to use a [Binary](#binary-market) or [Categorical](#categorical-market) Market respectively.

## Settlement

Settlement is something that happens one of two ways. The first is when a trader who holds [Shares](#shares) would like to cash out of their [Position](#position) on an expired and finalized [Market](#market). The other is the collection of a [Complete Set](#complete-set) which can happen before a market expires. The [Trading Fees](#trading-fees), which includes both the [Trading Fee](#trading-fee) and the [Reporting Fee](#reporting-fee), are only extracted during settlement.

## Shares

A Share is the ownership of a portion of a [Market's](#market) [Outcome's](#outcome) value. A [Complete Set](#complete-set) of Shares are created when both the [Maker](#maker) and [Taker](#taker) of an [Order](#order) send value to the market. Shares are settled (destroyed) when a Complete Set is sold back to the market.

## Taker

A Taker is someone who partially or fully [Fills](#fill-order) an [Open Order](#open-order) on the [Order Book](#order-book). Takers send currency or [Shares](#shares) to fill the Open Order and complete their half of the trade described in the [Order](#order).

## Topic

A Topic is a keyword used to categorize [markets](#market). All markets must have a topic, and are optionally allowed up to two sub-topics to further categorize the market. An example market for "Will the New York Giants win Super Bowl 100?" might have a Topic of "Sports" and sub-topics of "American Football" and "NFL". The Topics are set by the [Market Creator](#market-creator) when a new market is made and cannot be changed.

## Trading Fee

A Trading Fee is set by the [Market Creator](#market-creator) when he or she creates a new [Market](#market). Once the trading fee is set, it can never be increased only decreased. The Trading Fee must be between 0% and 50%. The Trading Fee and the [Reporting Fee](#reporting-fee) are both extracted at the same time whenever shares are settled on a market. Shares can be settled when a user amasses a [Complete Set](#complete-set) or when the market has been finalized and you want to close your open [Position](#position). The Trading Fee is designed to incentivize users to make popular markets as they stand to earn money if enough people trade on the market. They can then recoup their market creation cost and ideally turn a profit on posting interesting markets. The [Trading Fees](#trading-fees) are discussed in more details in the [Trading](#trading) section of the documentation.

## Winning Outcome

The Winning Outcome is the currently [Resolved](#market-resolution) [Outcome](#outcome) for a [Market](#market). In other words, it's the outcome who [Reporters](#reporters) wagered was the correct outcome of the market.
