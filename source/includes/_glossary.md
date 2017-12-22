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

A Binary Market is a [Market](#market) with only two [Outcomes](#outcome) (as well as [Invalid](#invalid-outcome), which is always a possible outcome). Binary Markets are for yes-or-no questions; if more potential outcomes are needed, a [Categorical](#categorical-market) or [Scalar](#scalar-market) Market should be used.

## Categorical Market

A Categorical Market is a [Market](#market) with more than 2 potential [Outcomes](#outcome), but no more than 8. As with all Markets, [Invalid] is also a potential Outcome, but it does not count toward the 8-outcome maximum. Categorical Markets are best for multiple choice questions, such as "Which team will win Tournament X?" or "What color tie will the U.S. President wear at the next presidential press conference?". If a Market is based around a simple yes-or-no question, it is better to use a [Binary Market](#binary-market). For a Market about what a particular stock price will be on a given date, a [Scalar Market](#scalar-market) should be used, as 8 potential outcomes would not be sufficient.

## Challenge

Challenge is used to describe the act of a [REP](#rep) holder [Staking](#dispute-stake) REP on a [Market](#market) [Outcome](#outcome) other than the [Tentative Outcome](#tenative-outcome). This REP goes toward filling the [Dispute Bond](#dispute-bond) in order to [Dispute](#dispute) or "challenge" the Tentative Outcome of a [Market Awaiting Finalization](#market-awaiting-finalization) before it is [Finalized](#finalized-market). In return for Staking on a given outcome, the REP holder will receive an amount of [Participation Tokens](#participation-token) proportionate to the amount Staked, which will allow the holder to receive fees from the Reward Pool at the end of the current Fee Window.

## Child Universe

A Child Universe is a [Universe](#universe) created after a [Market](#market) [Forks](#fork). Child Universes have a [Parent Universe](#parent-universe), which is the Universe where the [Forked Market](#forked-market) resides. [Locked Universes](#locked-universe) always have Child Universes because the Forking process causes the Universe containing the Forked Market to become Locked and creates the Child Universes.

## Complete Set

A Complete Set is a collection of [Shares](#shares) in every [Outcome](#outcome). Complete Sets are created when the [Creator](#order-creator) and [Filler](#order-filler) of an [Order](#order) both use currency to pay for the trade, as opposed to one or both parties using Shares to complete the [Trade](#trading). When both parties use shares to complete a trade, then a Complete Set will be formed and [Settled](#settlement) (destroyed). The cost in [attoETH](#atto-prefix) of a Complete Set for a particular [Market](#market) is determined by the [Number of Ticks](#number-of-ticks) for that Market. When Complete Sets are Settled, [Settlement Fees](#settlement-fees) are extracted from the value of the Complete Set and are paid proportionally by both parties. Therefore, users who get a larger payout from Settlement also pay a larger portion of the fees. The Settlement Fees extracted from Share Settlement go toward paying for the [Reporting](#report) system, in the form of a [Reporting Fee](#reporting-fee), and paying the [Market Creator](#market-creator) their set [Creator Fee](#creator-fee).

## Creator Fee

A Creator Fee is set by the [Market Creator](#market-creator) when he or she creates a new [Market](#market). Once the Creator Fee is set, it cannot be changed. The Creator Fee must be between 0% and 50%. The Creator Fee and the [Reporting Fee](#reporting-fee) are both extracted at the same time whenever [Shares](#shares) are [Settled](#settlement). Shares can be Settled when a user sells a [Complete Set](#complete-set) or when the Market has been [Finalized](#finalized-market) and a user wants to close an open [Position](#position). The Creator Fee is designed to incentivize users to make popular Markets as they stand to earn money if enough people trade on the Market. They can then recoup their Market creation cost and ideally turn a profit on posting interesting Markets. The [Settlement Fees](#settlement-fees) are discussed in more details in the [Trading](#trading) section of the documentation.

## Decentralized Oracle

The heart of Augur is its Decentralized Oracle, which allows users and smart contracts to propose questions to Augur and discover accurate information about the real world (based on how REP holders have voted on [Market](#market) [Outcomes](#outcomes)) without having to trust a single person, AI, or organization. Augur’s Oracle allows information to be migrated from the real world to a blockchain without relying on a centralized, trusted third party.

## Designated Report

A Designated Report occurs when a [Designated Reporter](#designated-reporter) [Stakes](#designated-reporter-stake) [REP](#rep) on a particular [Outcome](#outcome) in a [Market](#market). This Outcome then becomes the [Tentative Outcome](#tentative-outcome) for the Market, and the Market changes to the [Waiting for the Next Fee Window to Begin Phase](#waiting-for-the-next-fee Window to Begin Phase). 

<!-- TODO: Add explanation for how the exact Designated Report No-Show Gas Bond is calculated. (Need to verify if formula is gas to report * avg gas price in last window * 2) -->
## Designated Report No-Show Gas Bond

The Designated Report No-Show Gas Bond (or No-Show Gas Bond for brevity) is paid for using ETH by the [Market Creator](#market-creator) during [Market](#market) creation. This Bond is completely refunded to the Market Creator as soon as the [Designated Reporter](#designated-reporter) submits a [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase). This Bond is used in the event of a Designated Reporter failing to submit a Report during the Designated Reporting Phase and an [Initial Reporter](#initial-reporter) submitting a Report instead. When an Initial Reporter submits an [Initial Report](#initial-report), they are required to pay the gas cost. However, if the [Outcome](#outcome) of the [Initial Report](#initial-report) becomes the [Final Outcome](#final-outcome) of the Market, the Initial Reporter will receive the No-Show Gas Bond when the Market is [Finalized](#finalized-market).

<!-- TODO: Add explanation for how the exact Designated Report No-Show REP Bond is calculated (https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Universe.sol#L331). -->
## Designated Report No-Show REP Bond

The Designated Report No-Show REP Bond (or No-Show REP Bond for brevity) is paid for using [REP](#rep) by the [Market Creator](#market-creator) during [Market](#market) creation. If the [Designated Reporter](#designed-reporter) submits a [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase), the Bond is refunded to the Market Creator. If the Designated Reporter fails to Report during the Designated Reporting Phase, then the No-Show REP Bond is applied as Stake on the [Tentative Outcome](#tentative-outcome) Reported by the [Initial Reporter](#initial-reporter). If the Tentative Outcome selected by the Initial Reporter becomes the [Final Outcome](#final-outcome) of the Market, the Initial Reporter receives the No-Show REP Bond. If the Tentative Outcome selected by the Initial Reporter is [Disputed](#disputed) and then still becomes the [Final Outcome](#final-outcome) of the Market, the Initial Reporter receives the No-Show REP Bond plus an additional 50% of the Bond amount. This actually allows for someone to stake 0 REP for the [Initial Report](#initial-report) because the Bond is added to whatever is staked. This means someone without any REP has the potential to Report and if the Market is eventually [Finalized](#finalized-market) the way that person Reported, then they can earn REP without having to purchase any. (Note that they will have to pay the gas cost to submit the Report, but they will receive the Designated Report No-Show Gas Bond when the Market Finalizes, provided that the [Outcome](#outcome) they reported becomes the Final Outcome.)

The No-Show REP Bond is not a static value.  It rises as more Designated Reporters fail to Report within the Designated Reporting Phase and falls as more of them do Report within the Designated Reporting Phase. It is also calculated differently than the [Designated Reporter Stake](#designated-reporter-stake); however, their minimum values are calculated in the same way in order to guarantee a maximum of 20 7-day [Dispute Rounds](#dispute-round) before a [Fork](#fork) occurs.

The minimum value for the No-Show REP Bond is calculated as follows:

Since more than 1.25% of all existing REP must be Staked on an Outcome to cause a Fork to occur, that means that the threshold for causing a Fork is 11,000,000 REP * .0125 = 137,500 REP. In order for there to be a maximum of 20 7-day [Dispute Rounds](#dispute-round) before a [Fork](#fork) occurs, the minimum value for the No-Show REP Bond is 137,500 REP / 2<sup>20</sup> = 0.1311302185 REP.

## Designated Reporter

A Designated Reporter is a single Ethereum address designated to submit the [Tentative Outcome](#tentative-outcome) for a [Market](#market) during the [Designated Reporting Phase](#designated-reporting-phase). The Designated Reporter is set by the [Market Creator](#market-creator) during Market creation. All Markets must have a Designated Reporter.

<!-- TODO: Add explanation for how the exact Designated Reporter Stake is calculated. -->
## Designated Reporter Stake

When a [Designated Reporter](#designated-reporter) submits a [Report](#report), they must put up an amount of REP on an [Outcome](#outcome) that is equal to the Designated Reporter Stake. Note that this amount is calculated differently than the [No-Show REP Bond](#designated-report-no-show-rep-bond).

The Designated Reporter Stake is not a static value. It rises as more Designated Reporters Report incorrectly and falls as more of them report correctly. It is also calculated differently than the [No-Show REP Bond](#designated-reporter-no-show-rep-bond); however, their minimum values are calculated in the same way in order to guarantee a maximum of 20 7-day [Dispute Rounds](#dispute-round) before a [Fork](#fork) occurs.

The minimum value for the Designated Reporter Stake is calculated as follows:

Since more than 1.25% of all existing REP must be Staked on an Outcome to cause a Fork to occur, that means that the threshold for causing a Fork is 11,000,000 REP * .0125 = 137,500 REP. In order for there to be a maximum of 20 7-day [Dispute Rounds](#dispute-round) before a [Fork](#fork) occurs, the minimum value for the Designated Reporter Stake is 137,500 REP / 2^20 = 0.1311302185 REP.

## Designated Reporting

Designated Reporting is the first and fastest way that a [Market](#market) can be [Reported](#report) on. One Ethereum address will be responsible for submitting a [Tentative Outcome](#tentative-outcome) for the Market and will have 3 days to do so after a Market's [End Time](#end-time), known as the [Designated Reporting Phase](#designated-reporting-phase).

## Designated Reporting Phase

The Designated Reporting Phase lasts up to three (3) days and begins immediately following the [End Time](#end-time) of a [Market](#market). During this time, the [Designated Reporter](#designated-reporter) is expected to [Report](#report) a [Tentative Outcome](#tentative-outcome) for the Market. When the Designated Reporter submits a Report, the Market will enter the [Waiting for the Next Fee Window to Begin Phase](#waiting-for-the-next-fee-window-to-begin-phase). If the Designated Reporter fails to Report on the Market within the Designated Reporting Phase, then the Market goes to the [Initial Reporting Phase](initial-reporting-phase). When this happens, the [Market Creator](#market-creator) does not get refunded the [No-Show Bond](#no-show-bond). Instead, the [No-Show REP Bond](designated-report-no-show-REP-bond) goes toward covering the [Stake](#initial-reporter-stake) of the [Initial Reporter](#initial-reporter) when they submit the [Initial Report](#initial-report), and the [No-Show Gas Bond](Designated Report No-Show Gas Bond) goes to the Initial Reporter if the Tentative Outcome they Reported becomes the [Final Outcome](#final-outcome) of the Market.

## Dispute

Disputing is synonymous with the act of [Challenging](#challenge) a [Tenative Outcome](#tentative-outcome).

<!-- TODO: Add explanation of when users get their staked REP returned to them. -->
## Dispute Bond

When a [Market](#market) is in the [Dispute Round Phase](#dispute-round-phase), users may [Stake](#dispute-stake) [REP](#rep) on an [Outcome](#outome) other than the [Tentative Outcome](#tentative-outcome) if they believe the Tentative Outcome is incorrect. Doing so contributes to a Dispute Bond for that Outcome. Each Outcome other than the Tentative Outcome has its own Dispute Bond that can be filled. Dispute Bonds need not be paid in their entirety by a single user. Augur allows participants to crowdsource the Dispute Bonds. Any user who sees an incorrect Tentative Outcome can dispute that Outcome by Staking some REP on an Outcome other than the Tentative Outcome. If, collectively, some Outcome (other than the Tentative Outcome) receives enough Dispute Stake to fill its Dispute Bond, the current Tentative Outcome will be successfully [Disputed](#dispute). For information about how Dispute Stake gets returned to users, refer to the [Dispute Stake glossary entry](#dispute-stake).

The amount of REP required to fill a Dispute Bond is calculated as follows.

Let <i>A<sub>n</sub></i> denote the total Stake over all of this Market’s Outcomes at the beginning of Dispute Round <i>n</i>. Let &omega; be any Market Outcome other than the Market’s Tentative Outcome at the beginning of this Dispute Round. Let <i>S</i>(&omega;, <i>n</i>) denote the total amount of Stake on outcome &omega; at the beginning of dispute <i>n</i>. Then the size of the Dispute Bond needed to successfully Dispute the current Tentative Outcome in favor of the new Outcome &omega; during round <i>n</i> is denoted <i>B</i>(&omega;,<i>n</i>) and is given by:

<i>B</i>(&omega;,<i>n</i>)=2<i>A<sub>n</sub></i>-<i>3S</i>(&omega;,<i>n</i>)

## Dispute Round

A Dispute Round is one 7-day round out of a maximum of 20 such rounds that can occur during the [Dispute Round Phase](#dispute-round-phase) and before a [Fork](#fork) occurs in a [Market](#market). During a Dispute Round, [REP](#rep) holders who do not agree that the Market's [Tentative Outcome](#tentative-outcome) is accurate can [Stake](#dispute-stake) REP on a different [Outcome](#outcome) in an attempt to [Challenge](#challenge) it. If the Challenge is successful, the Tentative Outcome will be changed to the Outcome that was Staked on, or the Market will [Fork](#fork) if the current Dispute Round is the 20th round.

## Dispute Round Phase

The Dispute Round Phase is a seven (7) day window after a [Market](#market) has been [Reported](#report) on. During the Dispute Round Phase, [REP](#rep) holders who do not agree that the Market's [Tentative Outcome](#tentative-outcome) is accurate can [Stake](#dispute-stake) REP on a different [Outcome](#outcome) in an attempt to [Challenge](#challenge) it. If the Challenge is successful, the Tentative Outcome will be changed to the Outcome that was Staked on, or the Market will [Fork](#fork) if the current [Dispute Round](#dispute-round) is the 20th round. With each Dispute Round that successfully Challenges the Tentative Outcome, the next Dispute Round will require an even higher [Dispute Bond](#dispute-bond) to Challenge the Tentative Outcome. For details on how the Dispute Bond is calculated, refer to the the [Dispute Bond glossary entry](#dispute-bond).

## Dispute Stake

When a [Market](#market) is in the [Dispute Round Phase](#dispute-round-phase), users can Stake [REP](#rep) on any [Outcome](#outcome) other than the [Tenative Outcome](#tentative-outcome) and get an equivalant number of [Participation Tokens](#participation-tokens) in return. These Participation Tokens allow them to collect fees when the Market [Settles](#settlement). The amount of REP they Stake goes toward filling the [Dispute Bond](#dispute-bond).

All unsuccesful Dispute Stake is returned to the original owners at the end of every [Dispute Round](#dispute-round). All successful Dispute Stake is applied to the [Outcome](#outcome) it championed, and will remain there until the [Market](#market) is [Finalized](#finalized-market) (or until a [Fork](#fork) occurs in some other Augur Market). All Dispute Stake (whether successful or unsuccessful) will receive a portion of the [Reward Pool](#reward-pool) from the current [Fee Window](#fee-window).

For details on how the Dispute Bond is calculated, refer to the [Dispute Bond glossary entry](#dispute-bond).

## End Time

End Time is the date and time that a [Market](#market)'s event will have come to pass and should be known. After this date and time has passed the Market will get [Reported](#report) on and eventually [Finalized](#finalized-market).

## Fee Window

Augur’s [Reporting](#report) system runs on a cycle of consecutive 7-day long [Fee Windows](#fee-windows). Any fees collected by Augur during a given Fee Window are added to a [Reward Pool](#reward-pool) for that Fee Window. The Reward Pool is used to reward [REP](#rep) holders who participate in Augur’s Reporting process. REP holders who [Stake](#dispute-stake) REP during a given Fee Window receive the same number of [Participation Tokens](#participation-token) for that Fee Window. At the end of the Fee Window, the Reward Pool is paid out to holders of Participation Tokens in proportion to the number of Participation Tokens they hold. In this way, all REP holders who participate during a Fee Window will receive a portion of the fees collected during that Window. Participation includes [Designated](#designated-reporting) or [Initial Reporting](#initial-reporting), [Disputing](#dispute) an [Outcome](#outcome), or simply purchasing Participation Tokens directly.

## Fill Order

Filling an [Order](#order) is when a [Filler](#order-filler) provides what the [Creator](#order-creator) of the order is seeking in their [Order](#order). If a Filler only provides some of what the Creator wants then it's known as a partial fill. If the Filler provides exactly what the Creator requests then it's known as completely filling the order.

## Final Outcome

A Final Outcome is a [Tentative Outcome](#tentative-outcome) that is not [Challenged](#challenge) through a [Dispute Round Phase](#dispute-round-phase) or is determined through a [Fork](#fork). At this point, the [Market](#market) becomes [Finalized](#finalized-market) and moves to the [Finalized Phase](#finalized-phase). A Market's Final Outcome cannot be challenged or changed.

## Finalized Market

A Finalized Market is a [Market](#market) that has not had its [Tentative Outcome](#tentative-outcome) [Challenged](#challenge) through a [Dispute Round Phase](#dispute-round-phase), or has gone through the [Fork Phase](#fork-period). Its [Tentative Outcome](#tentative-outcome) is now considered final, and the Market will now allow [Share](#shares) holders to [Settle](#settlement) their Shares with the Market.

## Finalized Phase

This is the last phase a [Market](#market) can be in, and it occurs once the Market has been [Finalized](#finalized-market).

## First Report

The First Report is simply the first [Report](#report) placed on a [Market](#market). The First Report generally will come from the [Designated Reporter](#designed-reporter) for the Market, which would immediately refund the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) to the [Market Creator](#market-creator). In the event a Designated Reporter fails to Report then the Market is moved into the next available [Reporting Window](#reporting-window), along with the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond), for it's [First Report Round](#first-report-round). The first person to submit a Report and Stake at least 0 [REP](#rep) on their Report will have the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) added to their Stake and their Report transaction's gas cost covered by the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond). This provides an incentive to submit the First Report as the [First Reporter](#first-reporter) stands to gain the Designated Report No-Show REP Bond in REP if they Stake on the eventual [Final Outcome](#final-outcome).

## First Report Round

The First Report Round is the first [Reporting Round](#reporting-round) a [Market](#market) will go through if it requires Reporting Rounds to be [Resolved](#market-resolution).

## First Reporter

The First Reporter is the person who submits the [First Report](#first-report) on a [Market](#market). Generally the First Reporter will be the [Designated Reporter](#designated-reporter), however if the Designated Reporter fails to [Report](#report) then the First Reporter will become whoever submits the First Report during the [First Report Round](#first-report-round) for this Market.

## Fork

A Fork occurs if a [Market Awaiting Finalization](#market-awaiting-finalization) from an [Last Report Round](#last-report-round) of [Reporting Rounds](#reporting-round) is [Disputed](#dispute-bond). A Fork causes Augur to create multiple [Universes](#universe) of itself. Each Universe is empty except for the [Forked Market](#forked-market). There will be a Universe created for each possible [Outcome](#outcome) of the [Market](#market), including invalid, and the Markets will be [Finalized](#finalized-market) on each Universe. [REP](#rep) holders will need to choose which Universe they want to migrate their REP tokens too. Migration is one way and final. After sixty (60) days the [Fork Period](#fork-period) ends and the Universe with the most REP migrated too it will allow traders to [Settle](#settlement) [Shares](#shares) for the [Forked Market](#forked-market) and [Reporting Fees](#reporting-fee) will be paid out to [Reporters](#reporter) for that Universe only. The original Universe that contained the Forked Market is known as the [Parent Universe](#parent-universe) and is considered [Locked](#locked-universe). All of the new Universes created are known as [Child Universes](#child-universe). Forking will result in a new REP Token contract belonging to each Child Universe spawned by the Fork.

## Fork Period

The Fork Period, or Fork Phase, is a sixty (60) day period of time after a [Fork](#fork) has occurred.

## Forked Market

A Forked Market is a [Market](#market) who's [Tentative Outcome](#tentative-outcome) after a [Last Report Round](#last-report-round) is [Disputed](#dispute-bond) and causes a [Fork](#fork) to occur. A Fork will cause the creation of multiple [Universes](#universe) of Augur with the Forked Market having a different [Final Outcome](#final-outcome) in each Universe. The Universe that contained the Forked Market originally will become a [Locked Universe](#locked-universe).

## Genesis Universe

This is the first and only [Universe](#universe) to exist at Augur's launch, meaning it has no [Parent Universe](#parent-universe). All [Markets](#market) and [REP](#rep) exist in this Universe until Augur's first [Fork](#fork) occurs. Users are required to migrate their REP from the legacy REP contract into this Universe at launch in order for their REP to be used with Augur.

## Initial Report

When a [Market's](#market) [Designated Reporter](#designated-reporter) fails to submit a [Designated Report](designated-report), and another user submits a [Report](#report) instead, this Report is called the Initial Report, or [First Report](#first-report).

## Initial Reporter

In the event that a [Market's](#market) [Designated Reporter](#designated-reporter) fails to submit a [Designated Report](#designated-report), the first user to submit a [Report](#report) becomes the Initial Reporter, or [First Reporter](#first-reporter). The Initial Reporter can use the [No-Show Bond](#no-show-bond) to [Stake](#initial-reporter-stake) [REP](#rep) on a [Tentative Outcome](#tentative-outcome) and pay for gas costs when submitting the Initial Report.
 
## Initial Reporter Stake

When a [Market's](#market) [Designated Reporter](#designated-reporter) fails to submit a [Designated Report](designated-report), and the [Initial Reporter](#initial-reporter) submits a [Report](#report) instead, the [No-Show REP Bond](#designated-report-no-show-rep-bond) gets used to [Stake](#stake-token) on a [Tentative Outcome](#tentative-outcome). If that Tentative Outcome becomes the [Final Outcome](#final-outcome), the Initial Repoter will receive the No-Show REP Bond back

## Initial Reporting Phase

Description pending.

## Invalid Outcome

Description pending.

## Last Report Round

The Last Report Round is the final [Reporting Round](#reporting-round) in which a [Challenge](#challenge) to the [Tentative Outcome](#tentative-outcome) during the [Dispute Round Phase](#dispute-round-phase) will result in a [Fork](#fork) instead of another Reporting Round. Unlike previous Reporting Rounds, when a [Market](#market) in the Last Report Round enters the Dispute Round Phase and it's Tentative Outcome is Challenged then the Market becomes a [Forked Market](#forked-market) and the [Universe](#universe) becomes a [Locked Universe](#locked-universe).

## Locked Universe

A Locked Universe is a [Universe](#universe) that had a [Fork](#fork) occur within it and no longer allows the creation of new [Markets](#markets). All Markets within a Locked Universe remain tradable, as Markets never stop being tradable, even after [Finalizing](#finalized-market). [REP](#rep) holders within a Locked Universe are given a one time and final option to migrate their REP to one of the new universes created after a Fork locks a Market. There is no time constraint to how long a REP holder is allowed to wait to choose a Universe to migrate their REP to.

## Market

A Market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each Market created on the Augur network will have an automatically managed [Order Book](#order-book), which will allow users to buy and sell [Shares](#shares) of different outcomes of the Market. The [Market Creator](#market-creator) can set the [Creator Fee](#creator-fee) for the Market, which once set cannot be raised, which will determine their cut of all shares [Settled](#settlement) on the Market. The Market Creator also needs to specify a [Max Price](#maximum-display-price) and a [Min Price](#minimum-display-price) as well as the [Number of Ticks](#tick) for the Market. There are three different Market types supported by Augur, they are: [Binary](#binary-market), [Categorical](#categorical-market), and [Scalar](#scalar-market).

## Market Awaiting Finalization

Market Awaiting Finalization occurs when a [Market](#market) has been [Reported](#report) on and has a [Tentative Outcome](#tentative-outcome). Market's await finalization for a period of 3 days, which is the length of the [Dispute Round Phase](#dispute-round-phase), in which [REP](#rep) Holders are allowed to post a [Dispute Bond](#dispute-bond) to force another round of reporting for the Market Awaiting Finalization. If a Market's Tentative Outcome is not [Challenged](#challenge) for 3 days it becomes a [Finalized Market](#finalized-market) and can no longer be Challenged.

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
If the [Designated Reporter](#designated-reporter) submits a [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase) then the No-Show Bond is returned to the Market Creator. Otherwise, the No-Show Bond is given to the [Initial Reporter](#initial-reporter) to cover their gas costs and the [Stake](#stake-token) in their Reported [Outcome](#outcome).

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
and before that Market's event has come to pass. Typically, it is the most active period of trading. Once the event has occurred, the Market enters the [Designated Reporting Phase](#designated-reporting-phase).

## Tentative Outcome

The Tentative Outcome is the currently reported [Outcome](#outcome) for a [Market](#market) that is [Awaiting Finalization](#market-awaiting-finalization). In other words, it's the outcome [Reporters](#reporters) have staked their [REP](#rep) on to indicate that it is the correct Outcome of the Market. If a Market isn't [Challenged](#challenge) while Awaiting Finalization by a REP holder posting a [Dispute Bond](#dispute-bond) then the Market will become [Finalized](#finalized-market) and the Prosed Outcome will become a [Final Outcome](#final-outcome).

## REP

REP, also known as Reputation, Reputation Tokens, or REP Tokens, is the currency used by the Augur Decentralized Oracle System. REP is used to stake on a [Report](#report) on the [Outcome](#outcome) of a [Market](#market), Pay the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) in order to create a Market, and to purchase a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization). During the [Reporting Phase](#reporting-phase) of a [Reporting Window](#reporting-window) is where most REP holders will be using their REP to Report. You will be asked to stake REP on an Outcome based on how confident you are in that Outcome being the result of the Market. The Markets you will see during a Reporting Window are all past their [End Time](#end-time) and the Outcome should be determinable. If the outcome is not determinable you can Report it as Invalid. The more REP you stake, the larger the share of the [Reporting Fees](#reporting-fee) you will receive if you Report with the consensus. The two bonds purchased by REP, Dispute Bonds and the Designated Report No-Show REP Bond, are both fully refunded to the bond placer if things go smoothly. So the [Market Creator](#market-creator) immediately gets their REP bond back if the [Designated Reporter](#designated-reporter) does Report during the [Designated Reporting Phase](#designated-reporting-phase). Dispute Bonds are refunded to the Dispute Bond Owner if the Market's Tentative Outcome is changed from the one Challenged.

## Report

A Report, or Reporting, is the staking of [REP](#rep) on the [Outcome](#outcome) of a [Market](#market) that's passed its [End Time](#end-time) by a [Reporter](#reporter). The staking of REP is the act of Reporting. Reporting as a term can be used to describe the act of submitting a report for a single market or a number of markets. Reporting takes place during a [Reporting Window](#reporting-window). For a detailed breakdown of the Reporting System in Augur, see the [Reporting Section](#reporting).

## Reporter

A Reporter is a [REP](#rep) holder who
Stakes REP on the [Outcome](#outcome) of a [Market](#market) who's [End Time](#end-time) has come to pass and is [Awaiting Finalization](#market-awaiting-finalization). A [Designated Reporter](#designated-reporter) is assigned by the [Market Creator](#market-creator) during Market Creation for [Designated Reporting](#designated-reporting). REP holders will need to wait for a Market to enter a [Reporting Round](#reporting-round) of a [Reporting Window](#reporting-window) in order to [Report](#report) on the Outcome of the Market. Reporters are expected to Report accurately as the Market's event should have come to pass and the result should be known. To incentivize this, reporters who stake on the non [Final Outcome](#final-outcome) will lose their stake to the Reporters who did correctly Stake their REP on the Final Outcome.

## Reporting Fee

The Reporting Fee is used to help pay for Augur's Decentralized Oracle System. When [Shares](#shares) are [Settled](#settlement) (aka destroyed), before paying out to the share holders Augur will extract the [Settlement Fees](#settlement-fees), which includes the [Creator Fee](#creator-fee) and the Reporting Fee. The Reporting Fee is sent to the [Reporting Window](#reporting-window) that contains the [Market](#market) being traded on, and are later used to pay [REP](#rep) holders for [Reporting](#report) on the [Outcome](#outcome) of Markets.

## Reporting Period

The Reporting Period is combined time of the [Reporting Phase](#reporting-phase) and [Dispute Round Phase](#dispute-round-phase) of a [Reporting Window](#reporting-window). In other words, it's a term used to describe the total duration of a Reporting Window.

## Reporting Phase

The Reporting Phase occurs in the first twenty seven (27) days of a [Reporting Window]. During this phase, [Markets](#market) in [Reporting Rounds](#reporting-round) are [Reported](#report) on by [Reporters](#reporter). Following a Reporting Phase the Market should receive a Report to set a [Tentative Outcome](#tentative-outcome) and be moved into a [Dispute Round Phase](#dispute-round-phase) during which the Market is considered to be [Awaiting Finalization](#market-awaiting-finalization).

## Reporting Round

Reporting Rounds take place in a [Reporting Window](#reporting-window) during which [REP](#rep) holders Stake REP on the [Outcome](#outcome) of [Markets](#market) currently in a Reporting Round. Markets enter Reporting Rounds if the [Designated Reporter](#designated-reporter) fails to [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase) or if the Designated Reporter's [Tentative Outcome](#tentative-outcome) is [Challenged](#challenge) during the [Dispute Round Phase](#dispute-round-phase). Markets start in the [First Report Round](#first-report-round) which incentivizes a [First Report](#first-report) if the Market hasn't received a Report from the Designated Reporter but otherwise acts like any other Reporting Round. Reporting Rounds last the entirety of a Reporting Window and if the Market's Tentative Outcome is Challenged during the [Dispute Round Phase](#dispute-round-phase) of the Reporting Window, it is moved up to the next level of Reporting Rounds until eventually reaching the [Last Report Round](#last-report-round). A Challenge of the Tentative Outcome following a Last Report Round will cause a [Fork](#fork) to occur.

## Reporting Window

The Reporting Window is a period of 30 days in which [Markets](#markets) that have passed their [End Time](#end-time) are expected to be [Reported](#report) on by [Reporters](#reporter). Reporting Windows last for thirty (30) days and consist of two (2) phases, the [Reporting Phase](#reporting-phase) and the [Dispute Round Phase](#dispute-round-phase). The combined duration of the Reporting Phase and the Dispute Round Phase is known as the [Reporting Period](#reporting-period). The Reporting Phase lasts twenty seven (27) days in which Markets in a [Reporting Round](#reporting-round) are Reported on. The Dispute Round Phase lasts three (3) days and during this time any [REP](#rep) holder is allowed to post a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of the [Market Awaiting Finalization](#market-awaiting-finalization). Reporting Windows are occasionally referred to as "Reporting Cycles" or "Reporting Periods" as those were legacy terms for a Reporting Window during development of Augur.

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