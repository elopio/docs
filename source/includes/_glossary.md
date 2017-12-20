Glossary
========
<aside class="notice">The Glossary section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>
<!--
this section will include subsections for all terms used and descriptions of what they are and what they do. This is to avoid repeating them same information over and over again in function descriptions in the API section. This section should be easy to navigate and easy to link to specific concepts. To that end, each concept should have its own subsection.

Goals:
  - Easy to navigate
    - every term has its own "section" for easy linking
    - alphabetical
  - Human Readable language
 -->
This section of the documentation is dedicated to terms found and used throughout the rest of documentation. Below you will find sections about terms used in Augur. The goal is to explain everything that might be confusing in an easy-to-understand way.

## Ask Order

An Ask Order is an [Order](#order) indicating the desire of the [Order Creator](#order-creator) to sell [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of a [Bid Order](#bid-order).

## Atto- (Prefix)

Atto- is a unit prefix in the metric system denoting a factor of 10^−18, or 0.000000000000000001.  This prefix is used for a number of terms in Augur, including attoETH, attoREP, attoshares, attotoken, etc.

## Bid Order

A Bid Order is an [Order](#order) indicating the desire of the [Order Creator](#order-creator) to buy [Shares](#shares) of one or more [Outcomes](#outcome). This is the opposite of an [Ask Order](#ask-order).

## Binary Market

A Binary Market is a [market](#market) with only two [outcomes](#outcome), as well as Indeterminate which is always a possible outcome. Binary markets are for yes or no questions, if you need more than a yes or no then a [Categorical](#categorical-market) or [Scalar](#scalar-market) Market might fit your needs better.

## Categorical Market

A Categorical Market is a [market](#market) with more than 2 potential [outcomes](#outcome), but no more than 8. As with all markets, Indeterminate is also an outcome not included in the 8 outcome maximum. Categorical Markets are best for multiple choice type questions, which team wins a tournament or what color tie the President of the United States wears at his next press conference. If you just need a yes or no question, you will probably want to make a [Binary Market](#binary-market). If you wanted to guess the temperature in degrees on a certain day, you would probably want to use a [Scalar Market](#scalar-market) for that, as it would be very difficult to pin down 8 possibilities and it's not a yes or no question.

## Challenge

Challenge is used to describe the act of a [REP](#rep) holder posting a [Dispute Bond](#dispute-bond) to dispute or "challenge" the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization) before it is [Finalized](#finalized-market).

## Child Universe

A Child Universe is a [Universe](#universe) created after a [Fork](#fork). Child Universes have a [Parent Universe](#parent-universe) which is the Universe that had the Fork happen on it. [Locked Universes](#locked-universe) always have Child Universes because a Fork causes the Universe to be locked which then causes the creation of new Universes which are considered Children of the original, and now Locked, Universe.

## Complete Set

A Complete Set is a collection of [Shares](#shares) in every [Outcome](#outcome). Complete Sets are created when the [Creator](#order-creator) and [Filler](#order-filler) of an [Order](#order) both use currency to pay for the trade, as opposed to one or both parties using Shares to complete the trade. When both parties use shares to complete a trade then a Complete Set will be formed and settled (destroyed). The cost in [attoETH](#atto-prefix) of a Complete Set for a particular [Market](#market) is determined by the [Number of Ticks](#number-of-ticks) for that Market. When Complete Sets are [Settled](#settlement), [Settlement Fees](#settlement-fees) are extracted from the value of the Complete Set and are paid proportionally by both parties, so if you are going to get a larger payout from Settlement you will also pay the lions share of the fees. The Settlement Fees extracted will go toward paying for the reporting system, in the form of a [Reporting fee](#reporting-fee), and paying the [Market Creator](#market-creator) their set [Creator Fee](#trading-fee) from Share Settlement.

## Creator Fee

A Creator Fee is set by the [Market Creator](#market-creator) when he or she creates a new [Market](#market). Once the Creator Fee is set, it cannot be changed. The Creator Fee must be between 0% and 50%. The Creator Fee and the [Reporting Fee](#reporting-fee) are both extracted at the same time whenever [Shares](#shares) are [Settled](#settlement). Shares can be Settled when a user sells a [Complete Set](#complete-set) or when the Market has been [Finalized](#finalized-market) and a user wants to close an open [Position](#position). The Creator Fee is designed to incentivize users to make popular Markets as they stand to earn money if enough people trade on the Market. They can then recoup their market creation cost and ideally turn a profit on posting interesting Markets. The [Settlement Fees](#settlement-fees) are discussed in more details in the [Trading](#trading) section of the documentation.

## Decentralized Oracle

The heart of Augur is its Decentralized Oracle that allows users and smart contracts to propose questions to Augur and discover accurate information about the real world (based on how REP holders have voted on [Market](#market) [Outcomes](#outcomes)) without having to trust a single person, AI, or organization. Augur’s Oracle allows information to be migrated from the real world to a blockchain without relying on a centralized, trusted third party.

## Designated Dispute Phase

The Designated Dispute Phase is a three (3) day period immediately following a [Reported](#report) [Proposed Outcome](#proposed-outcome) by an [Designated Reporter](#designated-reporter) during the [Designated Report Phase](#designated-report-phase). During this time, any [REP](#rep) holder can post a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the Proposed Outcome of the [Market](#market), which moves the Market into the next available [Reporting Window](#reporting-window) for a [Reporting Round](#reporting-round), specifically the [First Report Round](#first-report-round). If a Dispute Bond is not posted during the Designated Dispute Phase then the Market is [Finalized](#finalized-market) and the Proposed Outcome becomes the [Final Outcome](#final-outcome). The Designated Dispute Phase has identical rules to a Reporting Window's [Dispute Phase](#dispute-phase) with the one exception being that an Designated Dispute Phase is not part of a Reporting Window.

## Designated Report No-Show Gas Bond

The Designated Report No-Show Gas Bond is paid for using ETH by the [Market Creator](#market-creator) during Market Creation. This Bond is completely refunded to the Market Creator as soon as the [Designated Reporter](#designated-reporter) submits a [Report](#report) during the [Designated Report Phase](#designated-report-phase). This Bond is used in the event of a Designated Reporter failing to submit a Report during the Designated Report Phase. The [Market](#market), as well as the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) and this Bond, are moved into the next [Reporting Window](#reporting-window) for [Reporting Rounds](#reporting-round), starting with the [First Report Round](#first-report-round). During the First Report Round the [First Reporter](#first-reporter)'s transaction to submit the [First Report](#first-report) on this Market is paid for using the Designated Report No-Show Gas Bond. If the transaction costs less than the amount of the Designated Report No-Show Gas Bond then the remaining ETH is sent to the First Reporter.

## Designated Report No-Show REP Bond

The Designated Report No-Show REP Bond is paid for using [REP](#rep) by the [Market Creator](#market-creator) during Market Creation. The Bond is refunded to the Market Creator if the [Designated Reporter](#designed-reporter) submits a [Report](#report) during the [Designated Report Phase](#designated-report-phase). If the Designated Reporter fails to Report during the Designated Report Phase then the Designated Report No-Show REP Bond is applied to the stake of the First Reporter for this [Market](#market) in the [Reporting Rounds](#reporting-round) during the next available [Reporting Window](#reporting-window). This actually allows for someone to stake 0 REP for the [First Report](#first-report) because the Bond is added to whatever is staked. This means someone without any REP has the potential to Report and if the Market is eventually [Finalized](#finalized-market) the way that person Reported then they can earn REP without having to buy it.

## Designated Report Phase

The Designated Report Phase is up to three (3) days in length and begins immediately following the [End Time](#end-time) of a [Market](#market). During this time the [Designated Reporter](#designated-reporter) is expected to [Report](#report) a [Proposed Outcome](#proposed-outcome) for the Market. When the Designated Reporter submits his/her/its response the Market will immediately enter an [Designated Dispute Phase](#designated-dispute-phase). If the Designated Reporter fails to Report on the Market within the Designated Report Phase then the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) is lost and applied to the First Reporter's stake and the Market is moved into the next available [Reporting Window](#reporting-window) for a [Reporting Round](#reporting-round). The Designated Report Phase should not be confused with a Reporting Window's [Reporting Phase](#reporting-phase) which is longer and only concerns [Reporting Rounds](#reporting-round).

## Designated Reporter

An Designated Reporter is a single address designated to submit the [Proposed Outcome](#proposed-outcome) for a [Market](#market) during [Designated Reporting](#designated-reporting). The Designated Reporter is set by the [Market Creator](#market-creator) during Market Creation. All Markets must have a Designated Reporter.

## Designated Reporter Stake

When a [Market](#market) is in the [Dispute Round Phase](#dispute-round-phase), users can stake [REP](#rep) on any [Outcome](#outcome) other than the [Tenative Outcome](#tentative-outcome) and get an equivalant number of [Participation Tokens](#participation-tokens) in return. These Participation Tokens allow them to collect fees when the Market [Settles](#settlement). The amount of REP they stake goes toward filling the [Dispute Bond](#dispute-bond).

## Designated Reporting

Designated Reporting is the first and fastest way that a [Market](#market) can be [Reported](#report) on. One address will be responsible for submitting a [Proposed Outcome](#proposed-outcome) for the Market and will have 3 days to do so after a Market's [End Time](#end-time), known as the [Designated Report Phase](#designated-report-phase). After the Report has been submit by the Designated [Reporter](#reporter) then a 3 day [Designated Dispute Phase](#designated-dispute-phase) begins where in any [REP](#rep) Holder can [Challenge](#challenge) the [Market Awaiting Finalization](#market-awaiting-finalization). If the [Dispute Bond](#dispute-bond) is posted then the Market is moved into the next [Reporting Window](#reporting-window) and will be subject to [Reporting Rounds](#reporting-round). Designated Reporting is independent of Reporting Windows.

## Dispute

Disputing is synonymous with the act of [Challenging](#challenge) a [Tenative Outcome](#tentative-outcome).

## Dispute Bond

A Dispute Bond is a bond posted to force another round of [Reporting](#report) if a [Reporter](#reporter) feels the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization) isn't accurate. If the [Market's](#market) Proposed Outcome is changed in the forced round of reporting then the poster of the dispute bond will get their money back for successfully challenging the false [Outcome](#outcome) of the Market.

## Dispute Bond Size

The size of a [Dispute Bond](#dispute-bond) is calculated as follows:

Let <i>A<sub>n</sub></i> denote the total Stake over all of a [Market’s](#market) [Outcomes](#outcome) at the beginning of [Dispute Round](#dispute-round) <i>n</i>. Let &omega; be any Market Outcome other than the Market’s [Tentative Outcome](#tentative-outcome) at the beginning of this Dispute Round. Let <i>S</i>(&omega;, <i>n</i>) denote the total amount of Stake on Outcome &omega; at the beginning of [Dispute](#dispute) <i>n</i>. Then the size of the Dispute Bond needed to successfully Dispute the current Tentative Outcome in favor of the new outcome &omega; during round <i>n</i> is denoted <i>B</i>(&omega;, <i>n</i>) and is given by:

<i>B</i>(&omega;, <i>n</i>) = 2<i>A<sub>n</sub></i>-3<i>S</i>(&omega;, <i>n</i>)

## Dispute Phase

A Dispute Phase is a three (3) day window after a [Market](#market) has been [Reported](#report) on before which the [Proposed Outcome](#proposed-outcome) becomes the [Final Outcome](#final-outcome). During this 3 day period, a [REP](#rep) Holder can post a [Dispute Bond](#dispute-bond) if they would like to force another round of Reporting for the Market. The Market will be moved into the next [Reporting Window](#reporting-window). Markets in the Dispute Phase are referred to as [Markets Awaiting Finalization](#market-awaiting-finalization).

## Dispute Round

Description pending.

## Dispute Round Phase

Description pending.

## Dispute Stake

Description pending.

## End Time

End Time is the date and time that a [Market](#market)'s event will have come to pass and should be known. After this date and time has passed the Market will get [Reported](#report) on and [Finalized](#finalized-market).

## Fee Window

Description pending.

## Fill Order

Filling an [Order](#order) is when a [Filler](#order-filler) provides what the [Creator](#order-creator) of the order is seeking in their order. If a Filler only provides some of what the Creator wants then it's known as a partial fill. If the Filler provides exactly what the Creator requests then it's known as completely filling the order.

## Final Outcome

A Final Outcome is a [Proposed Outcome](#proposed-outcome) that is not [Challenged](#challenge) through a [Dispute Phase](#dispute-phase) and the [Market](#market) becomes [Finalized](#finalized-market). A Market's Final Outcome cannot be challenged or changed.

## Finalized Market

A Finalized Market is a [Market](#market) that has [Awaited Finalization](#market-awaiting-finalization) and has not been disputed, the [Proposed Outcome](#proposed-outcome) is now considered final. This market will now allow [Share](#shares) holders to [settle](#settlement) their shares with the market.

## Finalized Phase

Description pending.

## First Report

The First Report is simply the first [Report](#report) placed on a [Market](#market). The First Report generally will come from the [Designated Reporter](#designed-reporter) for the Market, which would immediately refund the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) to the [Market Creator](#market-creator). In the event a Designated Reporter fails to Report then the Market is moved into the next available [Reporting Window](#reporting-window), along with the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond), for it's [First Report Round](#first-report-round). The first person to submit a Report and Stake at least 0 [REP](#rep) on their Report will have the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) added to their Stake and their Report transaction's gas cost covered by the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond). This provides an incentive to submit the First Report as the [First Reporter](#first-reporter) stands to gain the Designated Report No-Show REP Bond in REP if they Stake on the eventual [Final Outcome](#final-outcome).

## First Report Round

The First Report Round is the first [Reporting Round](#reporting-round) a [Market](#market) will go through if it requires Reporting Rounds to be [Resolved](#market-resolution).

## First Reporter

The First Reporter is the person who submits the [First Report](#first-report) on a [Market](#market). Generally the First Reporter will be the [Designated Reporter](#designated-reporter), however if the Designated Reporter fails to [Report](#report) then the First Reporter will become whoever submits the First Report during the [First Report Round](#first-report-round) for this Market.

## Fork

A Fork occurs if a [Market Awaiting Finalization](#market-awaiting-finalization) from an [Last Report Round](#last-report-round) of [Reporting Rounds](#reporting-round) is [Disputed](#dispute-bond). A Fork causes Augur to create multiple [Universes](#universe) of itself. Each Universe is empty except for the [Forked Market](#forked-market). There will be a Universe created for each possible [Outcome](#outcome) of the [Market](#market), including invalid, and the Markets will be [Finalized](#finalized-market) on each Universe. [REP](#rep) holders will need to choose which Universe they want to migrate their REP tokens too. Migration is one way and final. After sixty (60) days the [Fork Period](#fork-period) ends and the Universe with the most REP migrated too it will allow traders to [Settle](#settlement) [Shares](#shares) for the [Forked Market](#forked-market) and [Reporting Fees](#reporting-fee) will be paid out to [Reporters](#reporter) for that Universe only. The original Universe that contained the Forked Market is known as the [Parent Universe](#parent-universe) and is considered [Locked](#locked-universe). All of the new Universes created are known as [Child Universes](#child-universe). Forking will result in a new REP Token contract belonging to each Child Universe spawned by the Fork.

## Fork Period

The Fork Period is a sixty (60) day period of time after a [Fork](#fork) has occurred.

## Forked Market

A Forked Market is a [Market](#market) who's [Proposed Outcome](#proposed-outcome) after a [Last Report Round](#last-report-round) is [Disputed](#dispute-bond) and causes a [Fork](#fork) to occur. A Fork will cause the creation of multiple [Universes](#universe) of Augur with the Forked Market having a different [Final Outcome](#final-outcome) in each Universe. The Universe that contained the Forked Market originally will become a [Locked Universe](#locked-universe).

## Genesis Universe

This is the first and only [Universe](#universe) to exist at Augur's launch, meaning it has no [Parent Universe](#parent-universe). All [Markets](#market) and [REP](#rep) exist in this Universe until Augur's first [Fork](#fork) occurs. Users are required to migrate their REP from the legacy REP contract into this Universe at launch in order for their REP to be used with Augur.

## Initial Report

Description pending.

## Initial Reporter

Description pending.

## Initial Reporting Phase

Description pending.

## Invalid Outcome

Description pending.

## Last Report Round

The Last Report Round is the final [Reporting Round](#reporting-round) in which a [Challenge](#challenge) to the [Proposed Outcome](#proposed-outcome) during the [Dispute Phase](#dispute-phase) will result in a [Fork](#fork) instead of another Reporting Round. Unlike previous Reporting Rounds, when a [Market](#market) in the Last Report Round enters the Dispute Phase and it's Proposed Outcome is Challenged then the Market becomes a [Forked Market](#forked-market) and the [Universe](#universe) becomes a [Locked Universe](#locked-universe).

## Locked Universe

A Locked Universe is a [Universe](#universe) that had a [Fork](#fork) occur within it and no longer allows the creation of new [Markets](#markets). All Markets within a Locked Universe remain tradable, as Markets never stop being tradable, even after [Finalizing](#finalized-market). [REP](#rep) holders within a Locked Universe are given a one time and final option to migrate their REP to one of the new universes created after a Fork locks a Market. There is no time constraint to how long a REP holder is allowed to wait to choose a Universe to migrate their REP to.

## Market

A Market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each Market created on the Augur network will have an automatically managed [Order Book](#order-book), which will allow users to buy and sell [Shares](#shares) of different outcomes of the Market. The [Market Creator](#market-creator) can set the [Creator Fee](#creator-fee) for the Market, which once set cannot be raised, which will determine their cut of all shares [Settled](#settlement) on the Market. The Market Creator also needs to specify a [Max Price](#maximum-display-price) and a [Min Price](#minimum-display-price) as well as the [Number of Ticks](#tick) for the Market. There are three different Market types supported by Augur, they are: [Binary](#binary-market), [Categorical](#categorical-market), and [Scalar](#scalar-market).

## Market Awaiting Finalization

Market Awaiting Finalization occurs when a [Market](#market) has been [Reported](#report) on and has a [Proposed Outcome](#proposed-outcome). Market's await finalization for a period of 3 days, which is the length of the [Dispute Phase](#dispute-phase), in which [REP](#rep) Holders are allowed to post a [Dispute Bond](#dispute-bond) to force another round of reporting for the Market Awaiting Finalization. If a Market's Proposed Outcome is not [Challenged](#challenge) for 3 days it becomes a [Finalized Market](#finalized-market) and can no longer be Challenged.

## Market Creator

A Market Creator is a user who created a [Market](#market). They are charged a small fee to make a new Market but can determine the [Creator Fee](#creator-fee) for [Settlement](#settlement) of [Shares](#shares) on that Market. Market Creators are incentivized to create popular Markets so as to generate the most amount of [Settlement Fees](#settlement-fees) for themselves. Other information a Market requires is the actual question being purposed, the type of Market, the number of [Outcomes](#outcome), [End Time](#end-time), and a [Topic](#topic).

## Market Creator Mailbox

A Market Creator Mailbox is an address that belongs to the [Creator](#market-creator) of a [Market](#market). All of the fees that a Market Creator can collect (whether as ETH or as tokens) get sent to this address, where the Creator can then withdraw them. Funds that are potentially refunded to the Market creator (such as [Validity Bonds](#validity-bond), [Designated Report No-Show Gas Bonds](#designated-report-no-show-gas-bond), and [Designated Report No-Show REP Bonds](#designated-report-no-show-rep-bond)) are also sent to the Market Creator Mailbox.

## Market Resolution

Market Resolution is the process of [Finalizing](#finalized-market) a [Market](#market). [Designated Reporting](#designated-reporting), [Reporting Rounds](#reporting-round) and [Forks](#fork) are all examples of attempts at Market Resolution.

## Maximum Display Price

The Maximum Display Price (often seen as `maxDisplayPrice`) is the maximum price allowed for a share on a [market](#market). For [Binary](#binary-market) or [Categorical](#categorical-market) Markets this value is always 1, as in 1 ETH. [Scalar](#scalar-market) markets' Maximum Display Price would be the top end of the range set by the [Market Creator](#market-creator).

## Minimum Display Price

The Minimum Display Price (often seen as `minDisplayPrice`) is the minimum price allowed for a share on a [market](#market). For [Binary](#binary-market) or [Categorical](#categorical-market) Markets this value is always 0, as in 0 ETH. [Scalar](#scalar-market) markets' Minimum Display Price would be the bottom end of the range set by the [Market Creator](#market-creator).

## No-Show Bond

[Market Creators](#market-creator) must put up a No-Show Bond when creating a new [Market](#market). The No-Show Bond is comprised of two parts: the [No-Show Gas Bond](#designated-report-no-show-gas-bond) and the [No-Show REP Bond](#designated-report-no-show-rep-bond).
If the [Designated Reporter](#designated-reporter) submits a [Report](#report) during the [Designated Reporting Phase](#designated-report-phase) then the No-Show Bond is returned to the Market Creator. Otherwise, the No-Show Bond is given to the [Initial Reporter](#initial-reporter) to cover their gas costs and the [Stake](#stake-token) in their Reported [Outcome](#outcome).

## Number of Ticks

The Number of Ticks can be thought of as the number of possible prices, or [Ticks](#tick), between [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price) for a [Market](#market). It's also the amount of [attoETH](#atto-prefix) required to purchase a single [Complete Set](#complete-set) of indivisible [Shares](#shares) for a Market. When Shares are [Settled](#settlement) then each Complete Set will yield Number of Ticks [attoETH](#atto-prefix). The yield from the Complete Sets Settlement is what [Settlement Fees](#settlement-fees) are extracted from prior to paying out traders for their closed [Positions](#position). Settlement Fees are paid proportionally so that the trader set to receive more payout will have to pay more Fees. The price of an Order can be set to anywhere between 0 and the [Number of Ticks](#number-of-ticks) set for the Market.

## Open Order

An Open Order is an [Order](#order) that is currently on the [Order Book](#order-book) and has not been completely [Filled](#fill-order).

## Order

An Order can be thought of as the recorded interest of a user to execute a trade of some amount of [Shares](#shares) at a defined price point. Orders come in two types, [Bid Orders](#bid-order) and [Ask Orders](#ask-order), which indicate an attempt to buy or sell respectively. The [Creator](#order-creator) of the order will also need to escrow currency or shares in order to provide their half of the trade. The information stored in an Order is as follows: the type of order, the [Market](#market) the order is trading on, the [Outcome](#outcome) the order is concerned with buying or selling, the Order Creator's address, the price per share, the amount of shares to trade, what block number the order was created during, the amount of currency or Shares escrowed in the order by the Order Creator for their half of the trade.

## Order Book

The Order Book is the collection of all [Open Orders](#open-order) currently available for a [Market](#market). [Orders](#order) are placed on the order book by [Order Creators](#order-creator) and are [Filled](#fill-order) by [Order Fillers](#order-filler). Orders are divided up by which type, or side, of order they are [Bid](#bid-order) or [Ask](#ask-order). Orders are further divided up by [Outcome](#outcome).

## Order Creator

An Order Creator is the person who places an [Order](#order) on the [Order Book](#order-book). They escrow currency or [Shares](#shares) into their Order to buy or sell Shares of an [Outcome](#outcome) of a [Market](#market).

## Order Filler

An Order Filler either partially or fully [Fills](#fill-order) an [Open Order](#open-order) on the [Order Book](#order-book). Order Fillers send currency or [Shares](#shares) to fill the Open Order and complete their half of the trade described in the [Order](#order).

## Outcome

An outcome is a potential result of a [Market](#market)'s future event. For example, a market with a question of "Will it rain anywhere in New York City on November 1st, 2032 as reported by www.weather.com?" would have three potential Outcomes: Yes, No, and Invalid. Invalid would be an option if the world blew up before November 1st, 2032 and there was no New York City or www.weather.com to verify the Market's Outcome. More realistically this can happen for markets that have too vague of a question. A good example of a vague market that would most likely be voted invalid would be "Does God exist?" as no one has a definitive answer.

## Parent Universe

A Parent Universe is a [Universe](#universe) that has spawned [Child Universes](#child-universe) because a [Fork](#fork) had occurred on the Parent Universe and caused it to make new Universes. In other words, [Locked Universes](#locked-universe) are Parent Universes to the Universes created due to the Fork.

## Participation Token

Participation Tokens are used to determine how reporting fees are divided up. The more Participation Tokens a user buys (purchased with REP), the larger his or her share of the fees. The user gets back that REP after fees are distributed, so it essentially acts like a deposit. It's a way of encouraging [Reporters] (#reporter) to be active in the platform even when they're not needed, so they'll be around when they are needed. Buying Participation Tokens is not required. Participation Tokens can be purchased anytime before a [Reporting Window] (#reporting-window) ends. They are only used in the case that only [Designated Reporters] (#designated-reporter) are needed for reporting (everyone shows up) and no one disputes the Designated Reporters. Participation Tokens are purchased after all [Markets] (#market) in the Reporting Window have finalized.

## Payout Distribution Hash

The Payout Distribution Hash is a sha3 hash of the [Payout Set](#payout-set). When a [Market](#market) is [Awaiting Finalization](#market-awaiting-finalization) it is said to have a tentative Winning Payout Distribution Hash. Once the Market is Finalized then the tentative hash becomes the Winning Payout Distribution Hash. Payout Distribution Hashes of [Forked Markets](#forked-markets) are used as identifiers for [Child Universes](#child-universe) and [Parent Universes](#parent-universe).

## Payout Set

A Payout Set, sometimes referred to as the "Payout Numerator" in contract methods, is an array with a length equal to the number of [Outcomes](#outcome) for a [Market](#market). Each value in the Array is required to be 0 or a positive number that does not exceed the [Number of Ticks](#number-of-ticks) for the Market. Further, the total sum of all the values contained within the Payout Set array should be equaled to the Number of Ticks for the Market. A quick example, on a [Binary Market](#binary-market) with 1000 [Ticks](#tick), if we wanted to submit a [Report](#report) that stakes [REP](#rep) on Outcome `0`, we would submit a Payout Set that looks like this `[1000, 0]`. Payout Sets are a breakdown of how Markets should payout, or the Payout Distribution, when [Finalized](#finalized-market), so in the above example, only shares of Outcome `0` will payout on the Finalized Market. Valid Payout Sets are hashed using sha3, and becomes a [Payout Distribution Hash](#payout-distribution-hash).

## Position

A Position is the amount of [Shares](#share) that is owned (a long position) or borrowed and then sold (a short position) by an individual. A position can be profitable or unprofitable, depending on [Market](#market) movements. Positions can be Open or Closed. An Open Position simply means you currently own the shares, where as a closed position means you have now redeemed your shares and have cashed out for currency. Closing a short positions means you are buying the Shares of the [Outcome](#outcome) you are short on, where as closing a long position means selling the Shares you own.

## Pre-Reporting Phase

This is the phase that occurs after a [Market's](#market) creation
and before that Market's event has come to pass. Typically, it is the most active period of trading. Once the event has occurred, the Market enters the [Designated Reporting Phase](#designated-report-phase).

## Proposed Outcome

The Proposed Outcome is the currently reported [Outcome](#outcome) for a [Market](#market) that is [Awaiting Finalization](#market-awaiting-finalization). In other words, it's the outcome [Reporters](#reporters) have staked their [REP](#rep) on to indicate that it is the correct Outcome of the Market. If a Market isn't [Challenged](#challenge) while Awaiting Finalization by a REP holder posting a [Dispute Bond](#dispute-bond) then the Market will become [Finalized](#finalized-market) and the Prosed Outcome will become a [Final Outcome](#final-outcome).

## REP

REP, also known as Reputation, Reputation Tokens, or REP Tokens, is the currency used by the Augur Decentralized Oracle System. REP is used to stake on a [Report](#report) on the [Outcome](#outcome) of a [Market](#market), Pay the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) in order to create a Market, and to purchase a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization). During the [Reporting Phase](#reporting-phase) of a [Reporting Window](#reporting-window) is where most REP holders will be using their REP to Report. You will be asked to stake REP on an Outcome based on how confident you are in that Outcome being the result of the Market. The Markets you will see during a Reporting Window are all past their [End Time](#end-time) and the Outcome should be determinable. If the outcome is not determinable you can Report it as Invalid. The more REP you stake, the larger the share of the [Reporting Fees](#reporting-fee) you will receive if you Report with the consensus. The two bonds purchased by REP, Dispute Bonds and the Designated Report No-Show REP Bond, are both fully refunded to the bond placer if things go smoothly. So the [Market Creator](#market-creator) immediately gets their REP bond back if the [Designated Reporter](#designated-reporter) does Report during the [Designated Report Phase](#designated-report-phase). Dispute Bonds are refunded to the Dispute Bond Owner if the Market's Proposed Outcome is changed from the one Challenged.

## Report

A Report, or Reporting, is the staking of [REP](#rep) on the [Outcome](#outcome) of a [Market](#market) that's passed its [End Time](#end-time) by a [Reporter](#reporter). The staking of REP is the act of Reporting. Reporting as a term can be used to describe the act of submitting a report for a single market or a number of markets. Reporting takes place during a [Reporting Window](#reporting-window). For a detailed breakdown of the Reporting System in Augur, see the [Reporting Section](#reporting).

## Reporter

A Reporter is a [REP](#rep) holder who
Stakes REP on the [Outcome](#outcome) of a [Market](#market) who's [End Time](#end-time) has come to pass and is [Awaiting Finalization](#market-awaiting-finalization). A [Designated Reporter](#designated-reporter) is assigned by the [Market Creator](#market-creator) during Market Creation for [Designated Reporting](#designated-reporting). REP holders will need to wait for a Market to enter a [Reporting Round](#reporting-round) of a [Reporting Window](#reporting-window) in order to [Report](#report) on the Outcome of the Market. Reporters are expected to Report accurately as the Market's event should have come to pass and the result should be known. To incentivize this, reporters who stake on the non [Final Outcome](#final-outcome) will lose their stake to the Reporters who did correctly Stake their REP on the Final Outcome.

## Reporting Fee

The Reporting Fee is used to help pay for Augur's Decentralized Oracle System. When [Shares](#shares) are [Settled](#settlement) (aka destroyed), before paying out to the share holders Augur will extract the [Settlement Fees](#settlement-fees), which includes the [Creator Fee](#creator-fee) and the Reporting Fee. The Reporting Fee is sent to the [Reporting Window](#reporting-window) that contains the [Market](#market) being traded on, and are later used to pay [REP](#rep) holders for [Reporting](#report) on the [Outcome](#outcome) of Markets.

## Reporting Period

The Reporting Period is combined time of the [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase) of a [Reporting Window](#reporting-window). In other words, it's a term used to describe the total duration of a Reporting Window.

## Reporting Phase

The Reporting Phase occurs in the first twenty seven (27) days of a [Reporting Window]. During this phase, [Markets](#market) in [Reporting Rounds](#reporting-round) are [Reported](#report) on by [Reporters](#reporter). Following a Reporting Phase the Market should receive a Report to set a [Proposed Outcome](#proposed-outcome) and be moved into a [Dispute Phase](#dispute-phase) during which the Market is considered to be [Awaiting Finalization](#market-awaiting-finalization).

## Reporting Round

Reporting Rounds take place in a [Reporting Window](#reporting-window) during which [REP](#rep) holders Stake REP on the [Outcome](#outcome) of [Markets](#market) currently in a Reporting Round. Markets enter Reporting Rounds if the [Designated Reporter](#designated-reporter) fails to [Report](#report) during the [Designated Report Phase](#designated-report-phase) or if the Designated Reporter's [Proposed Outcome](#proposed-outcome) is [Challenged](#challenge) during the [Designated Dispute Phase](#designated-dispute-phase). Markets start in the [First Report Round](#first-report-round) which incentivizes a [First Report](#first-report) if the Market hasn't received a Report from the Designated Reporter but otherwise acts like any other Reporting Round. Reporting Rounds last the entirety of a Reporting Window and if the Market's Proposed Outcome is Challenged during the [Dispute Phase](#dispute-phase) of the Reporting Window, it is moved up to the next level of Reporting Rounds until eventually reaching the [Last Report Round](#last-report-round). A Challenge of the Proposed Outcome following a Last Report Round will cause a [Fork](#fork) to occur.

## Reporting Window

The Reporting Window is a period of 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). Reporting Windows last for thirty (30) days and consist of two (2) phases, the [Reporting Phase](#reporting-phase) and the [Dispute Phase](#dispute-phase). The combined duration of the Reporting Phase and the Dispute Phase is known as the [Reporting Period](#reporting-period). The Reporting Phase lasts twenty seven (27) days in which Markets in a [Reporting Round](#reporting-round) are Reported on. The Dispute Phase lasts three (3) days and during this time any [REP](#rep) holder is allowed to post a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the [Proposed Outcome](#proposed-outcome) of the [Market Awaiting Finalization](#market-awaiting-finalization). Reporting Windows are occasionally referred to as "Reporting Cycles" or "Reporting Periods" as those were legacy terms for a Reporting Window during development of Augur.

## Reward Pool

Description pending.

## Scalar Market

A Scalar Market is a [Market](#market) with a range for potential [outcomes](#outcome). A scalar market example might be "According to www.weather.com, what will the temperature in Fahrenheit be at SFO on January 3rd, 2062 at 1:00pm?". In this example market, we might set the [minDisplayPrice](#minimum-display-price) of the market to -50 and the [maxDisplayPrice](#maximum-display-price) to 150. This would allow for the market to [Settle](#settlement) on any number between the two. Sometimes you don't need a range of potential outcomes, only a simple yes/no or a small number of choices, in these cases you would want to use a [Binary](#binary-market) or [Categorical](#categorical-market) Market respectively.

## Settlement

Settlement is something that happens one of two ways. The first is when a trader who holds [Shares](#shares) would like to cash out of their [Position](#position) on a [Finalized Market](#finalized-market). The other is the collection of a [Complete Set](#complete-set) which can happen before a Market Finalizes. The [Settlement Fees](#settlement-fees), which includes both the [Creator Fee](#creator-fee) and the [Reporting Fee](#reporting-fee), are only extracted during settlement.

## Settlement Fees

Settlement Fees are fees extracted when a [Complete Set](#complete-set) is [Settled](#settlement). It's the combination of the [Creator Fee](#creator-fee) and the [Reporting Fee](#reporting-fee).

## Shares

A Share is the ownership of a portion of a [Market's](#market) [Outcome's](#outcome) value. A [Complete Set](#complete-set) of Shares are created when both the [Creator](#order-creator) and [Filler](#order-filler) of an [Order](#order) send currency to the market to complete an [Order](#order). Shares are settled (destroyed) when a Complete Set is sold back to the market.

## Stake Token

A Stake Token is used to represent the amount of Staked [REP](#rep) a [Reporter](#reporter) has on an [Outcome](#outcome) for their [Report](#report). Stake Tokens have a 1:1 cost ratio to REP, so 100 [attoREP](#atto-prefix) would buy you 100 [attoStakeTokens](#atto-prefix).

## Tentative Outcome

Description pending.

## Tick

A Tick is the smallest recognized amount by which a price of a security or future may fluctuate. Ticks are each individually a potential price point for a [Share](#shares) of an [Outcome](#outcome) for a [Market](#market) between its [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price). When a [Market Creator](#market-creator) creates a new Market they are asked to enter the [Number of Ticks](#number-of-ticks) for the Market. This number represents how much [attoETH](#atto-prefix) a [Complete Set](#complete-set) of Shares will cost to buy for this Market. A [Scalar Market](#scalar-market) with a Minimum Price of -10 and a Maximum Price of 30 could have a number of ticks set to 4000. This would mean that to purchase a Complete Set for this Market, you would need to spend 4000 [attoETH](#atto-prefix). The [Settlement](#settlement) of a Complete Set of Shares will yield 4000 attoeth, which [Settlement Fees](#settlement-fees) are then extracted from prior to payout. It also indicates that there are 4000 valid price points between -10 and 30 in this Market, which means an [Order](#order) with a price of 1.24 or 20.5 is valid for this Market, but a price of 5.725 would be invalid.

## Topic

A Topic is a keyword used to categorize [markets](#market). All markets must have a topic, and are optionally allowed up to two sub-topics to further categorize the Market. An example Market for "Will the New York Giants win Super Bowl 100?" might have a Topic of "Sports" and sub-topics of "American Football" and "NFL". The Topics are set by the [Market Creator](#market-creator) when a new market is made and cannot be changed.

## Universe

A Universe is a version of Augur. All [Markets](#market) created on Augur belong to a Universe. Augur will launch with only one Universe, but more can be created in the rare event of a [Fork](#fork). The Universe in which a Fork occurs will become a [Locked Universe](#locked-universe) and new Universes will be created, one for each [Outcome](#outcome) of the [Forked Market](#forked-market). After a Fork, [REP](#rep) holders can choose to migrate their REP to one of the new Universes created. They don't have to migrate, but Locked Universes do not allow the creation of new Markets, and therefor there will be no Markets to [Report](#report) on in the future and no fees to earn from Reporting. All Universes of Augur are equally valid, even after a Fork has been resolved, and can continue to operate after the [Fork Period](#fork-period) ends. Universes contain only the Forked Market with a [Final Outcome](#final-outcome) set to one of the possible outcomes, with a Universe created for each possibility, and no other Markets to begin with.

## Validity Bond

The Validity Bond is paid by the [Market Creator](#market-creator) during Market Creation. The bond is paid in ETH and is refunded to the Market Creator if the [Final Outcome](#final-outcome) of the [Market](#market) is not invalid.

## Waiting for the Next Fee Window to Begin Phase

Description pending.

## Winning Universe

Description pending.