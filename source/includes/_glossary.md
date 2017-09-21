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

All Reporting is the third level of the [Reporting](#report) system, second if the [Market](#market) never had a set [Designated Reporter](#designated-reporter). If a Market has gone through [Limited Reporting](#limited-reporting) and while [Awaiting Finalization](#market-awaiting-finalization) is [Challenged](#challenge) then the Market will be moved into the upcoming [Reporting Window](#reporting-window) and have it's state changed to All Reporting. Every Reporter is expected to report on All Reporting Markets during a Reporting Window's [Reporting Phase](#reporting-phase) in which they are [Registered](#registration-token) to Report. The Reporting Phase lasts for 27 days and is followed by a 3 day [Dispute Phase](#dispute-phase) where a [Dispute Bond](#dispute-bond) can be posted by any [REP](#rep) Holder to force the last reporting system state, a [Fork](#fork). All Reporting takes place within a Reporting Window.

## Ask Order

An Ask Order is an [Order](#order) indicating the desire of the [Maker](#maker) to sell [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of a [Bid Order](#bid-order).

## Designated Dispute Phase

The Designated Dispute Phase is a three (3) day period immediately following a [Reported](#report) [Proposed Outcome](#proposed-outcome) by an [Designated Reporter](#designated-reporter) during the [Designated Report Phase](#designated-report-phase). During this time, any [REP](#rep) holder can post a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the Proposed Outcome of the [Market](#market), which moves the Market into the next available [Reporting Window](#reporting-window) for a round of [Limited Reporting](#limited-reporting). If a Dispute Bond is not posted during the Designated Dispute Phase then the Market is [Finalized](#finalized-market) and the Proposed Outcome becomes the [Final Outcome](#final-outcome). The Designated Dispute Phase has identical rules to a Reporting Window's [Dispute Phase](#dispute-phase) with the one exception being that an Designated Dispute Phase is not part of a Reporting Window.

## Designated Report Phase

The Designated Report Phase is up to three (3) days in length and begins immediately following the [End Time](#end-time) of a [Market](#market) with an [Designated Reporter](#designated-reporter). During this time the Designated Reporter is expected to [Report](#report) a [Proposed Outcome](#proposed-outcome) for the Market. When the Designated Reporter submits his/her/it's response the Market will immediately enter an [Designated Dispute Phase](#designated-dispute-phsae). If the Designated Reporter fails to Report on the Market within the Designated Report Phase then the [Designated Reporter Bond](#designated-reporter-bond) is lost and the Market is moved into the next available [Reporting Window](#reporting-window) for a round of [Limited Reporting](#limited-reporting). The Designated Report Phase only occurs for [Designated Reporting](#designated-reporting) Markets and should not be confused with a Reporting Window's [Reporting Phase](#reporting-phase) which is longer and only concerned Limited and [All Reporting](#all-reporting).

## Designated Reporter

An Designated Reporter is a single address designated to submit the [Proposed Outcome](#proposed-outcome) for a [Market](#market) during [Designated Reporting](#designated-reporting). The Designated Reporter is set by the [Market Creator](#market-creator) during Market Creation. If no Designated Reporter is set then the market will use [Limited Reporting](#limited-reporting) as it's first attempt to be [Finalized](#finalized), instead of Designated Reporting.

## Designated Reporter Bond

The Designated Reported Bond is paid by the [Market Creator](#market-creator) during Market Creation. It's only required if the Market Creator intends to assign an [Designated Reporter](#designated-reporter) for their [Market](#market). This is refunded to the Market Creator if the Designated Reporter's [Proposed Outcome](#proposed-outcome) matches the [Final Outcome](#final-outcome) for the Market.

## Designated Reporting

Designated Reporting is the first and fastest way that a [Market](#market) can be [Reported](#report) on. One address will be responsible for submitting a [Proposed Outcome](#proposed-outcome) for the Market and will have 3 days to do so after a Market's [End Time](#end-time). After the Report has been submitted by the Designated [Reporter](#reporter) then a 3 day [Dispute Phase](#dispute-phase) begins where in any [REP](#rep) Holder can post a bond to dispute the [Market Awaiting Finalization](#market-awaiting-finalization). If the [Dispute Bond](#dispute-bond) is posted then the market is moved into the next [Reporting Window](#reporting-window) and will be subject to [Limited Reporting](#limited-reporting). Designated Reporting is independent of Reporting Windows.

## Bid Order

A Bid Order is an [Order](#order) indicating the desire of the [Maker](#maker) to buy [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of an [Ask Order](#ask-order).

## Binary Market

A Binary Market is a [market](#market) with only two [outcomes](#outcome), as well as Indeterminate which is always a possible outcome. Binary markets are for yes or no questions, if you need more than a yes or no then a [Categorical](#categorical-market) or [Scalar](#scalar-market) market might fit your needs better.

## Branch

A Branch can be thought of as a separate universe of Augur where the [Proposed Outcome](#proposed-outcome) submitted for a [All Reporting](#all-reporting) [Market](#market) is [Disputed](#dispute-bond) and multiple versions of Augur are needed to resolve the dispute. Each version of Augur is a different Branch, where each version will feature a copy of Augur's state with the one difference being the Proposed Outcome of the Market that caused the [Fork](#fork). Once the Fork occurs, [REP](#REP) holders will have to choose which branch to migrate their REP to. Migration is a one way action and is final. The Market that caused the Fork will be considered [Finalized](#finalized-market) in all branches, however only the branch that has the most REP migrated to it after 60 days will allow traders to [Settle](#settlement) [Shares](#shares) and payout [Reporters](#reporters) their [Reporting Fees](#reporting-fee).

## Categorical Market

A Categorical Market is a [market](#market) with more than 2 potential [outcomes](#outcome), but no more than 8. As with all markets, Indeterminate is also an outcome not included in the 8 outcome maximum. Categorical Markets are best for multiple choice type questions, which team wins a tournament or what color tie the President of the United States wears at his next press conference. If you just need a yes or no question, you will probably want to make a [Binary Market](#binary-market). If you wanted to guess the temperature in degrees on a certain day, you would probably want to use a [Scalar Market](#scalar-market) for that, as it would be very difficult to pin down 8 possibilities and it's not a yes or no question.

## Challenge

Challenge is used to describe the act of a [REP](#rep) holder posting a [Dispute Bond](#dispute-bond) to dispute or "challenge" the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization) before it is [Finalized](#finalized-market).

## Complete Set

A Complete Set is a collection of [Shares](#shares) in every [Outcome](#outcome). Complete Sets are created when the [Maker](#maker) and [Taker](#taker) of an [Order](#order) both use currency to pay for the trade, as opposed to one or both parties using Shares to complete the trade. When both parties use shares to complete the trade then a Complete Set will be formed and settled (destroyed). The range of the [market](#market), ([maxDisplayPrice](#maximum-display-price) - [minDisplayPrice](#minimum-display-price)) - [Trading Fees](#trading-fees) ETH will then be paid out proportionally to both the Maker and Taker based on their respective Shares value at the time of settlement. The Trading Fees extracted will go toward paying for the reporting system and paying the [Market Creator](#market-creator) their set [Trading Fee](#trading-fee) from share settlement.

## Dispute Bond

A Dispute Bond is a bond posted to force another round of [Reporting](#report) if a [Reporter](#reporter) feels the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization) isn't accurate. If the [Market's](#market) Proposed Outcome is changed in the forced round of reporting then the poster of the dispute bond will get their money back for successfully challenging the false [Outcome](#outcome) of the Market.

## Dispute Phase

A Dispute Phase is a three (3) day window after a [Market](#market) has been [Reported](#report) on before which the [Proposed Outcome](#proposed-outcome) becomes the [Final Outcome](#final-outcome). During this 3 day period, a [REP](#rep) Holder can post a [Dispute Bond](#dispute-bond) if they would like to force another round of Reporting for the Market. The Market will be moved into the next [Reporting Window](#reporting-window). Markets in the Dispute Phase are referred to as [Markets Awaiting Finalization](#market-awaiting-finalization).

## End Time

End Time is the date and time that a [Market](#market)'s event will have come to pass and should be known. After this date and time has passed the Market will get [Reported](#report) on and [Finalized](#finalized-market).

## Fill Order

Filling an [Order](#order) is when a [Taker](#taker) provides what the [Maker](#maker) of the order is seeking in their order. If a taker only provides some of what the Maker wants then it's known as a partial fill. If the Taker provides exactly what the Maker requests then it's known as completely filling the order.

## Final Outcome

A Final Outcome is a [Proposed Outcome](#proposed-outcome) that is not [Challenged](#challenge) through a [Dispute Phase](#dispute-phase) and the [Market](#market) becomes [Finalized](#finalized-market). A Market's Final Outcome cannot be challenged or changed.

## Finalized Market

A Finalized Market is a [Market](#market) that has [Awaited Finalization](#market-awaiting-finalization) and has not been disputed, the [Proposed Outcome](#proposed-outcome) is now considered final. This market will now allow [Share](#shares) holders to [settle](#settlement) their shares with the market.

## Fork

A Fork occurs if a [Market Awaiting Finalization](#market-awaiting-finalization) from an [All Reporting](#all-reporting) state of [Reporting](#report) is [Disputed](#dispute-bond). A Fork causes Augur to create multiple [Branches](#branch) of itself, where each branch is a copy of the current state of Augur with the only difference being the [Final Outcome](#final-outcome) of the [Forked Market](#forked-market). There will be a branch created for each possible [Outcome](#outcome) of the [Market](#market), including invalid, and the markets will be [Finalized](#finalized-market) on each Branch. [REP](#REP) holders will need to choose which branch they want to migrate their REP tokens too. Migration is one way and final. After sixty (60) days the [Fork Period](#fork-period) ends and the branch with the most REP migrated too it will allow traders to [Settle](#settlement) [Shares](#shares) for that Market and [Reporting Fees](#reporting-fee) will be paid out to [Reporters](#reporter) for that branch.

## Fork Period

The Fork Period is a sixty (60) day window of time after a [Fork](#fork) has occurred.

## Forked Market

A Forked Market is a [Market](#market) who's [Proposed Outcome](#proposed-outcome) after an [All Reporting](#all-reporting) state is [Disputed](#dispute-bond) and causes a [Fork](#fork) to occur. A fork will cause multiple [Branches](#branch) of Augur with the Forked Market having a different [Final Outcome](#final-outcome) in each Branch.

## Limited Reporting

Limited Reporting is the second level of [Reporting](#report) and is the first attempt at [Market Finalization](#finalized-market) if an [Designated Reporter](#designated-reporter) hasn't been set by the [Market Creator](#market-creator). Limited Reporting means the [Market](#market) needs to be reported on by a certain amount of [Reporters](#reporter) who will stake their REP on the [Proposed Outcome](#proposed-outcome) of their choosing. Limited Reporting has a [Reporting Phase](#reporting-phase) that lasts for twenty seven (27) days and has a three (3) day [Dispute Phase](#dispute-phase) following it. If a Limited Report's Proposed Outcome is [challenged](#challenge) the Market is moved into the [All Reporting](#all-reporting) state and attached to the next [Reporting Window](#reporting-window). Limited Reporting takes place within a Reporting Window.

## Maker

A Maker is the creator of an [Order](#order) that is placed on the [Order Book](#order-book). They escrow currency or [Shares](#shares) into their Order in order to buy or sell Shares of an [Outcome](#outcome) of a [Market](#market).

## Market

A market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each market created on the Augur network will have an automatically managed [Order Book](#order-book), which will allow users to buy and sell [Shares](#shares) of different outcomes of the market. The [Market Creator](#market-creator) can set the [Trading Fee](#trading-fee) for the market, which once set cannot be raised, which will determine their cut of all shares [Settled](#settlement) on the Market. There are three different Market types supported by Augur, they are: [Binary](#binary-market), [Categorical](#categorical-market), and [Scalar](#scalar-market).

## Market Awaiting Finalization

Market Awaiting Finalization occurs when a [Market](#market) has been [Reported](#report) on and has a [Proposed Outcome](#proposed-outcome). Market's await finalization for a period of 3 days, which is the length of the [Dispute Phase](#dispute-phase), in which [REP](#rep) Holders are allowed to post a [Dispute Bond](#dispute-bond) to force another round of reporting for the Market Awaiting Finalization. If a Market's Proposed Outcome is not [Challenged](#challenge) for 3 days it becomes a [Finalized Market](#finalized-market) and can no longer be Challenged.

## Market Creator

A Market Creator is a user who created a [market](#market). They are charged a small fee to make a new market but can determine the [Trading Fee](#trading-fee) for [Settlement](#settlement) of [Shares](#shares) on that market. Market Creators are incentivized to create popular markets so as to generate the most amount of Settlement fees for themselves. Other information a market requires is the actual question being purposed, the type of market, the number of [Outcomes](#outcome), [End Time](#end-time), and a [Topic](#topic).

## Market Resolution

Market Resolution is the process of [Finalizing](#finalized-market) a [Market](#market). [Designated Reporting](#designated-reporting), [Limited Reporting](#limited-reporting), [All Reporting](#all-reporting), and [Forks](#fork) are all examples of attempts to get Market Resolution.

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

An outcome is a potential result of a [Market](#market)'s future event. For example, a market with a question of "Will it rain anywhere in New York City on November 1st, 2032 as reported by www.weather.com?" would have three potential [Outcomes](#outcome): Yes, No, and Invalid. Invalid would be an option if the world blew up before November 1st, 2032 and there was no New York City or www.weather.com to verify the Market's Outcome. More realistically this can happen for markets that have too vague of a question. A good example of a vague market that would most likely be voted invalid would be "Does God exist?" as no one has a definitive answer.

## Position

A Position is the amount of [Shares](#share) that is owned (a long position) or borrowed and then sold (a short position) by an individual. A position can be profitable or unprofitable, depending on [Market](#market) movements. Positions can be Open or Closed. An Open Position simply means you currently own the shares, where as a closed position means you have now redeemed your shares and have cashed out for currency. Closing a short positions means you are buying the Shares of the [Outcome](#outcome) you are short on, where as closing a long position means selling the Shares you own.

## Proposed Outcome

The Proposed Outcome is the currently reported [Outcome](#outcome) for a [Market](#market) that is [Awaiting Finalization](#market-awaiting-finalization). In other words, it's the outcome [Reporters](#reporters) have staked their [REP](#rep) on to indicate that it is the correct Outcome of the Market. If a Market isn't [Challenged](#challenge) while Awaiting Finalization by a REP holder posting a [Dispute Bond](#dispute-bond) then the Market will become [Finalized](#finalized-market) and the Prosed Outcome will become a [Final Outcome](#final-outcome).

## Registration Token

A Registration Token is purchasable by [REP](#REP) holders for REP and is used as a sort of ticket to participate in the upcoming [Reporting Window](#reporting-window) that the Registration Token belongs to. Once the Reporting Window has started, you will need the Registration Token to be able to report. When the [Reporter](#reporter) finishes [Reporting](#report) they are able to redeem their Registration Token for the REP spent to purchase it. The Registration Token cannot be redeemed once the reporting window it belongs to has passed if the Reporter did not Report and the deposit is lost. The Registration Token is designed as a sort of deposit to ensure that Reporters who sign up to Report in the upcoming Reporting Window actually show up and participate in the reporting process, or lose their deposit.

## REP

REP, also known as Reputation, Reputation Tokens, or REP Tokens, is the currency used by the Augur Decentralized Oracle System. REP is used to purchase a [Registration Token](#registration-token) for an upcoming [Reporting Window](#reporting-window) and to [Report](#report) on the [outcome](#outcome) of [Markets](#market). Once you have registered for a Reporting Window, when the time comes to report you will be shown markets that need to be finalized. You will be asked to wager REP on an outcome based on how confident you are in that outcome being the result of the question asked in the market. The markets you will see for reporting are all past their [End Time](#end-time) and the outcome should be determinable. If the outcome is not determinable you can wager your rep into the Indeterminate outcome. The more REP you wager, the larger the share of the [Reporting Fees](#reporting-fee) you will receive if you report with the consensus.

## Report

A Report, or Reporting, is the staking of [REP](#REP) on the [Outcome](#outcome) of a [Market](#market) that's passed it's [End Time](#end-time) by a [Reporter](#reporter). The staking of REP is the act of Reporting. Reporting as a term can be used to describe the act of submitting a report for a single market or a number of markets. Reporting takes place during a [Reporting Window](#reporting-window). For a detailed breakdown of the Reporting System in Augur, see the [Reporting Section](#reporting).

## Reporter

A Reporter, or Registered Reporter, is a [REP](#REP) holder who
Stakes REP on the [Outcome](#outcome) of a [Market](#market) who's [End Time](#end-time) has come to pass and is [Awaiting Finalization](#market-awaiting-finalization). A Reporter can be assigned by the [Market Creator](#market-creator) during Market Creation for [Designated Reporting](#designated-reporting), otherwise REP holders will need to purchase a [Registration Token](#registration-token) for an upcoming [Reporting Window](#reporting-window) in order to [Report](#report) on the Outcome of Markets. Reporters are expected to Report accurately as the Market's event should have come to pass and the result should be known.

## Reporting Fee

The Reporting Fee is used to help pay for Augur's Decentralized Oracle System. When [Shares](#shares) are [Settled](#settlement) (aka destroyed), before paying out to the share holders Augur will extract the [Trading Fees](#trading-fees), which includes the [Trading Fee](#trading-fee) and the Reporting Fee. The Reporting Fees are sent to the [Reporting Window](#reporting-window) that contains the [market](#market) being traded on, and are later used to pay [REP](#rep) holders for [Reporting](#report) on the Outcome of Markets.

## Reporting Phase

The Reporting Phase occurs in the first twenty seven (27) days of a [Reporting Window]. During this phase, [Limited Reporting](#limited-reporting) and [All Reporting](#all-reporting) [Markets](#market) are [Reported](#report) on by [Reporters](#reporter). Following a Reporting Phase the Market should have a set [Proposed Outcome](#proposed-outcome) and be moved into a [Dispute Phase](#dispute-phase) during which the Market is considered to be [Awaiting Finalization](#market-awaiting-finalization).

## Reporting Window

The Reporting Window is a period of 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). Reporting Windows last for thirty (30) days and consist of two (2) phases, the [Reporting Phase](#reporting-phase) and the [Dispute Phase](#dispute-phase). The Reporting Phase lasts twenty seven (27) days in which [Limited Reporting](#limited-reporting) and [All Reporting](#all-reporting) Markets are Reported on. The Dispute Phase lasts three (3) days and during this time any [REP](#REP) holder is allowed to post a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the [Proposed Outcome](#proposed-outcome) of the [Market Awaiting Finalization](#market-awaiting-finalization). Reporting Windows are occasionally referred to as "Reporting Cycles" or "Reporting Periods" as those were legacy terms for a Reporting Window during development of Augur.

## Scalar Market

A Scalar Market is a [Market](#market) with a range for potential [outcomes](#outcome). A scalar market example might be "According to www.weather.com, what will the temperature in Fahrenheit be at SFO on January 3rd, 2062 at 1:00pm?". In this example market, we might set the [minDisplayPrice](#minimum-display-price) of the market to -50 and the [maxDisplayPrice](#maximum-display-price) to 150. This would allow for the market to [Settle](#settlement) on any number between the two. Sometimes you don't need a range of potential outcomes, only a simple yes/no or a small number of choices, in these cases you would want to use a [Binary](#binary-market) or [Categorical](#categorical-market) Market respectively.

## Settlement

Settlement is something that happens one of two ways. The first is when a trader who holds [Shares](#shares) would like to cash out of their [Position](#position) on a [Finalized Market](#finalized-market). The other is the collection of a [Complete Set](#complete-set) which can happen before a Market Finalizes. The [Trading Fees](#trading-fees), which includes both the [Trading Fee](#trading-fee) and the [Reporting Fee](#reporting-fee), are only extracted during settlement.

## Shares

A Share is the ownership of a portion of a [Market's](#market) [Outcome's](#outcome) value. A [Complete Set](#complete-set) of Shares are created when both the [Maker](#maker) and [Taker](#taker) of an [Order](#order) send currency to the market to complete an [Order](#order). Shares are settled (destroyed) when a Complete Set is sold back to the market.

## Taker

A Taker is someone who partially or fully [Fills](#fill-order) an [Open Order](#open-order) on the [Order Book](#order-book). Takers send currency or [Shares](#shares) to fill the Open Order and complete their half of the trade described in the [Order](#order).

## Topic

A Topic is a keyword used to categorize [markets](#market). All markets must have a topic, and are optionally allowed up to two sub-topics to further categorize the Market. An example Market for "Will the New York Giants win Super Bowl 100?" might have a Topic of "Sports" and sub-topics of "American Football" and "NFL". The Topics are set by the [Market Creator](#market-creator) when a new market is made and cannot be changed.

## Trading Fee

A Trading Fee is set by the [Market Creator](#market-creator) when he or she creates a new [Market](#market). Once the trading fee is set, it can never be increased, only decreased. The Trading Fee must be between 0% and 50%. The Trading Fee and the [Reporting Fee](#reporting-fee) are both extracted at the same time whenever [Shares](#shares) are [Settled](#settlement). Shares can be settled when a user amasses a [Complete Set](#complete-set) or when the market has been [Finalized](#finalized-market) and you want to close your open [Position](#position). The Trading Fee is designed to incentivize users to make popular markets as they stand to earn money if enough people trade on the market. They can then recoup their market creation cost and ideally turn a profit on posting interesting markets. The [Trading Fees](#trading-fees) are discussed in more details in the [Trading](#trading) section of the documentation.

## Validity Bond

The Validity Bond is paid by the [Market Creator](#market-creator) during Market Creation. The bond is paid in ETH and is refunded to the Market Creator if the [Final Outcome](#final-outcome) of the [Market](#market) is not invalid.
