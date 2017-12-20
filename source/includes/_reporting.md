
Reporting
=========
<aside class="notice">The Reporting section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Once the event underlying an Augur [Market](#market) occurs, the [Outcome](#outcome) needs to be determined in order for the Market to [Finalize](#finalized-market) and begin [Settlement](#settlement). The Outcomes of events are reported by Augur’s [Decentralized Oracle](#decentralized-oracle), which consists of profit-motivated [Reporters](#reporter) reporting on the Outcome of events. Anyone who owns [REP](#rep) tokens may participate in the [Reporting](#report) process by submitting a [Designated Report](#designated-report) or [Initial Report](#initial-report), or by [Disputing](#dispute) [Tentative Outcomes](#tentative-outcomes). Reporters whose [Reports](#report) concur with consensus are financially rewarded, while those whose Reports do not concur with consensus are financially penalized.

Fee Windows
-----------

Augur’s reporting system runs on a cycle of consecutive 7-day long [Fee Windows](#fee-window). Any [Reporting Fees](#reporting-fee) collected by Augur during a given Fee Window are added to a [Reward Pool](#reward-pool) for that Fee Window. The Reward Pool is used to reward [REP](#rep) holders who participate in Augur’s reporting process. REP holders who [Stake](#dispute-stake) REP during a given Fee Window receive that number of [Participation Tokens](#participation-token) for that Fee Window. At the end of the Fee Window, the Reward Pool is paid out to holders of Participation Tokens in proportion to the number of Participation Tokens they hold.

In this way, all REP holders who participate during a Fee Window will receive a portion of the fees collected during that window. Participation includes [Designated Reporting](#designated-reporting) or [Initial Reporting](#initial-reporting), [Disputing](#dispute) an Outcome, or simply purchasing Participation Tokens directly.

Reporting States
----------------

<a href="images/Detailed Reporting Diagram.png"><img src="images/Detailed Reporting Diagram.png"></a>

The Reporting Diagram above is a high level overview of [Reporting](#report) implemented by Augur. There are two ways for a [Market](#market) to attempt to [Finalize](#finalized-market): if the [Proposed Outcome](#proposed-outcome) was not disputed during a [Dispute Round](#dispute-round), or (rarely) during a [Fork](#fork). Augur Markets have 7 potential states after creation. The potential states, or “phases”, of a Market are as follows:

1. Pre-Reporting
2. Designated Reporting
3. Initial Reporting
4. Waiting for the Next Fee Window to Begin
5. Dispute Round
6. Fork
7. Finalized

### Pre-Reporting

The [Pre-Reporting Phase](#pre-reporting-phase) (1) is the time period where the Market's [End Time](#end-time) has not come to pass, generally this is the most active trading period. Once the End Time has come to pass, the Market enters the [Designated Reporting Phase](#designated-reporting-phase) (2).

### Designated Reporting

When creating a Market, the [Market Creator](#market-creator) is required to choose a [Designated Reporter](#designated-reporter) and post a [No-Show Bond](#no-show-bond). During the Designated Reporting Phase (2), the Market’s Designated Reporter has at most three days to Report on the [Outcome](#outcome) of the event. If the Designated Reporter fails to Report within the allotted 3 days, then the Market Creator forfeits the No-Show Bond, and the Market automatically enters the [Initial Reporting Phase](#initial-reporting-phase) (3).

If the Designated Reporter submits a Report on time, then the No-Show Bond is returned to the Market Creator. The Designated Reporter is required to post the [Designated Reporter Stake](#designated-reporter-stake) on its Reported Outcome, which it will forfeit if the Market finalizes to any outcome other than the one they Reported. As soon as the Designated Reporter submits its Report, the Market enters the Waiting for the Next Fee Window to Begin Phase (4), and the Reported Outcome becomes the Market’s [Tentative Outcome](#tentative-outcome).

### Initial Reporting

If the Designated Reporter fails to Report within the allotted 3 days, then the Market Creator forfeits the No-Show Bond, and the Market immediately enters the Initial Reporting Phase. As soon as the Market enters the Initial Reporting Phase, every Augur participant has the opportunity to report the Outcome of the Market. In the event that the Designated Reporter fails to report, we refer to the first participant who Reports the Outcome of a Market as the Market’s [Initial Reporter](#initial-reporter).

The Market’s Initial Reporter receives the No-Show Bond that was forfeited by the Market Creator when the Designated Reporter failed to Report. The [No-Show Gas Bond](#designated-report-no-show-gas-bond) is given directly to the Initial Reporter to cover their gas costs. The [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) is given to the Initial Reporter in the form of Stake on their Reported Outcome.

The Initial Reporter does not need to Stake any of their own [REP](#rep) when Reporting the Outcome of the Market. Because the No-Show REP Bond is applied as Stake on the Initial Reporter’s chosen Outcome, they may claim the No-Show REP Bond only if their Reported Outcome ultimately agrees with consensus. In this way, any Market whose Designated Reporter fails to Report is expected to have its Outcome reported by someone very soon after entering the Initial Reporting Phase.

Once an [Initial Report](#initial-report) has been received, the Reported Outcome becomes the Market’s Tentative Outcome, and the Market enters the [Waiting for the Next Fee Window to Begin Phase](#waiting-for-the-next-fee-window-to-begin-phase) (4).

### Waiting for the Next Fee Window to Begin

Once the market receives its First Report -- whether by the Designated Reporter or by an Initial Reporter from the community -- the Market enters the Waiting for the Next Fee Window to Begin Phase. During this phase, Reporting for the Market is on hold until end of the current [Fee Window](#fee-window). Once the current Fee Window comes to an end, the Market enters the [Dispute Round Phase](#dispute-round-phase) (5).

### Dispute Round

The [Dispute Round](#dispute-round) is a 7-day period, during which any REP holder has the opportunity to [Dispute](#dispute) the Market’s Tentative Outcome. A Dispute consists of staking REP (referred to as [Dispute Stake](#dispute-stake) in this context) on a some outcome other than the Market’s current Tentative Outcome. A Dispute is successful if the total amount of Dispute Stake on some outcome meets the [Dispute Bond Size](#dispute-bond-size) required for the current round. For details on how the Dispute Bond Size is calculated, refer to the Dispute Bond Size [Glossary entry](#dispute-bond-size).

The [Dispute Bonds](#dispute-bond) need not be paid in their entirety by a single user. Augur allows participants to crowdsource the Dispute Bonds. Any user who sees an incorrect Tentative Outcome can Dispute that Outcome by Staking some REP on some Outcome other than the Tentative Outcome. If, collectively, some Outcome (other than the Tentative Outcome) receives enough Dispute Stake to fill its Dispute Bond, the current Tentative Outcome will be successfully Disputed.

In the case of a successful Dispute, the Market will either undergo another Dispute Round or it will enter the [Fork Phase](#fork-period) (6). If the size of the filled Dispute Bond is greater than 1.25% of all REP in existence, then the market will enter the [Fork Phase](#fork-period). If the size of the filled Dispute bond is less than 1.25% of all REP in existence, then the newly chosen Outcome becomes the market’s new Tentative Outcome, and the market undergoes another Dispute Round.

If no Dispute is successful during the 7-day Dispute Round, the Market enters the [Finalized State](#finalized-phase) (7) with its Tentative Outcome accepted as the [Final Outcome](#final-outcome).

All unsuccesful Dispute Stake is returned to the original owners at the end of every Dispute Round. All successful Dispute Stake is applied to the Outcome it championed, and will remain there until the Market is Finalized (or until a [Fork](#fork) occurs in some other Augur Market). All Dispute Stake (whether successful or unsuccessful) will receive a portion of the reward pool from the current Fee Window.

### Fork

The Fork Phase is a special state that lasts up to 60 days and is intended to be an exceedingly rare occurrence. The outcome of a Fork cannot be Disputed. The Disputed Market that caused the Fork, referred to as the [Forking Market](#forked-market), enters the [Finalized State](#finalized-phase) (7) at the end of the Fork Phase. For more information on the Forking process, refer to the [Forking Details](#forking-details) section.

### Finalized

A Market enters the Finalized State anytime it passes through a 7-day Dispute Round without having its Tentative Outcome successfully Disputed. The Outcome of a Fork cannot be Disputed and is always considered final at the end of the Forking Phase. Once a Market is Finalized, traders can [Settle](#settlement) their [Positions](#position) directly with the Market. When a Market enters the Finalized State, we refer to its chosen Outcome as the Final Outcome.

Designated Reporting Details
----------------------------

During the creation of a Market, a [Market Creator](#market-creator) must assign a [Designated Reporter](#designated-reporter). [Designated Reporting](#designated-reporting) is designed to be a quick way to bring about a [Market Resolution](#market-resolution). This is pleasing to Traders who want to [Settle](#settlement) their [Shares](#shares) and collect their earnings as soon as possible after a Market's [End Time](#end-time) has passed. The [Designated Reporting Phase](#designated-reporting-phase) begins immediately following the End Time of a Market. The result of Designated Reporting is still [Challengeable](#challenge) with a [Dispute Bond](#dispute-bond) so if the Designated Reporter does incorrectly [Report](#report) the [Outcome](#outcome) of the [Market](#market) it can be adjudicated by [Dispute Rounds](#dispute-round). With the dispute system in place, we don't actually lose any security or truth as an incorrect [Proposed Outcome](#proposed-outcome) for a Market can be Challenged.

The [Designated Reporting Phase](#designated-reporting-phase) lasts a maximum of three (3) days and ends immediately when the Designated Reporter submits their Report and the [Proposed Outcome](#proposed-outcome) is set for the Market.

If the Designated Reporter fails to Report within the Designated Reporting Phase, the No-Show REP Bond, the No-Show Gas Bond and the Market is moved into the [Initial Reporting Phase](#initial-reporting-phase). These No-Show Bonds are then used to help pay for the gas costs and add to the [REP](#rep) [Staked](#initial-reporter-stake) by the [Initial Reporter](#initial-reporter).

If the Designated Reporter does successfully Report within the Designated Reporting Phase, the [No-Show REP Bond](#designated-report-no-show-rep-bond) and the [No-Show Gas Bond](#designated-report-no-show-gas-bond) is refunded to the Market Creator immediately and the Market enters the [Designated Dispute Phase](#designated-dispute-phase) which lasts for three (3) days. During this 3 day period any [REP](#rep) holder can post a [Dispute Bond](#dispute-bond) to Challenge the Proposed Outcome Reported by the Designated Reporter. If a Dispute Bond is posted the Market is moved into the next available Reporting Window for First Round Reporting. If the Market is left unchallenged for the duration of the Designated Dispute Phase then the Market's Proposed Outcome becomes the [Final Outcome](#final-outcome) and the Market is considered [Finalized](#finalized-market) allowing the Settlement of Shares.

Fee Window Details
---------------------------

[Fee Windows](#fee-window) continually occur and last for seven (7) days. During a Fee Window, any [REP](#rep) holder is able to [Stake](#dispute-stake) REP on any [Outcome](#outcome) other than the [Tentative Outcome](#tentative-outcome). By doing so, they receive an equal number of [Participation Tokens](#participation-tokens) in return, and the REP they Stake goes toward to a [Dispute Bond](#dispute-bond) to [Challenge](#challenge) the Tentative Outcome of the [Market](#market). In order to earn fees as a REP holder in the current Fee Window, users need to participate in the Market's [Dispute Round](#dispute-round) in some way, such as submitting a [Designated Report](#designated-report) or [Initial Report](#initial-report), [Disputing](#dispute) the Tentative Outcome (i.e., staking on an outcome other than the Tentative Outcome), or simply purchasing [Participation Tokens](#participation-token) directly.

Any fees collected by Augur during a given Fee Window are added to a [Reward Pool](#reward-pool) for that Fee Window. The Reward Pool is used to reward REP holders who participate in Augur’s reporting process. At the end of the Fee Window, the Reward Pool is paid out to holders of Participation Tokens in proportion to the number of Participation Tokens they hold.

Markets are placed into the next available Fee Window once the [Designated Reporter](#designated-reporter) or [Initial Reporter](#initial-reporter) submits a [Report](#report). For example, if a Market's Designated Report is submitted at 12:00 AM EST on January 3rd, 2053 and a Fee Window was just started on January 1st 2053, the Market would move into the next Fee Window that would be starting on January 8th, 2053, or 7 days since the start of the January 1st Window.

Any REP holder can participate in a Fee Window. In fact, in some cases, someone without any REP is able to successfully Report on Markets in the [Initial Reporting Phase](#initial-reporting-phase). This case only occurs when the Market's Designated Reporter fails to Report during the [Designated Reporting Phase](#designated-reporting-phase). In this situation, the first person to submit a Report can do so and Stake 0 REP on the Report. This is the true because the No-Show REP Bond is added to the Initial Reporter's Stake, so in this case the Stake can be 0 as it will get the bond added to it. This creates a race to be the Initial Reporter so as to attempt to get free REP from reporting correctly and earning the No-Show REP Bond.

Forking Details
---------------

The [Market Resolution](#market-resolution) method of last resort is the [Fork](#fork). Forks are initiated when a [Market](#market) has its [Tentative Outcome](#tentative-outcome) successfully [Disputed](#dispute) during a [Dispute Round](#dispute-round) by filling a [Dispute Bond](#dispute-bond) greater than 1.25% of all [REP](#rep) in existence. We refer to such a Market as a [Forking Market](#forked-market). 

When a Fork is initiated, the usual 7-day [Fee window](#fee-window) is interrupted, and a 60-day [Forking Period](#fork-period) begins. 

Forking is a very disruptive process and is intended to be rare. They are disruptive because Augur needs to assume its [Decentralized Oracle](#decentralized-oracle) is under attack if a Fork occurs. Since Augur assumes it's under attack, [Reporting](#report) for all other non-[Finalized Markets](#finalized-market) is put on hold until the end of the 60-day Forking Period. The Forking Period is much longer than the usual Fee Window so that there can be ample time for REP holders and exchanges to prepare for a Fork. The Forking process has final jurisdiction; a Fork’s outcome cannot be disputed.

Every Augur Market exists in some Augur [Universe](#universe). When Augur first launches, there will be only one Universe — the [Genesis Universe](#genesis-universe). Users will be required to migrate their REP from the legacy REP contract into the Genesis Universe in order for their REP to be used with Augur. Importantly, REP can be used to Report on [Outcomes](#outcome) (and thus earn fees) only if it exists in the same Universe as the Market on which it is reporting. When Augur first launches, all Markets and all REP will exist in the Genesis Universe.

When a Market Forks, new Universes are created. Forking creates a new [Child Universe](#child-universe) for each possible Outcome of the Forking Market (including the [Invalid Outcome](#invalid-outcome)). A [Binary Market](#binary-market), for example, has 3 possible outcomes: A, B, and Invalid. Thus a Binary Forking Market will create 3 new Child Universes: Universe A, Universe B, and Universe Invalid. Initially, these newly created Universes are empty; they contain no markets or REP tokens.

Once a Fork is initiated, the [Parent Universe](#parent-universe) becomes permanently [Locked](#locked-universe). Markets are always tradable, no matter what state they are in or what Universe. However, while trading can still take place in the Parent Universe, no new Markets may be created, and no reporting rewards may be paid out there. In order for Markets or tokens in the Locked Universe to be useful, they must first be migrated to a Child Universe.

Holders of REP tokens in the Parent Universe may migrate their tokens to a Child Universe of their choice. This choice should be considered carefully, because migration is one-way; it cannot be reversed. Tokens cannot be sent from one sibling Universe to another. Migration is a permanent commitment of REP tokens to a particular Market Outcome. REP tokens sent to different Child Universes ought to be considered entirely separate currencies, and exchanges ought to list them as such.

When a Fork is initiated, all REP [Staked](#dispute-stake) on all non-Forking Markets is unstaked so that it is free to be migrated to a Child Universe during the Forking Period.

Whichever Child Universe receives the most migrated REP by the end of the 60-day Forking Period becomes the winning Universe, and its corresponding Outcome becomes the Final Outcome of the Forking Market. All un-Finalized Markets in the Parent Universe are automatically migrated to the [Winning Universe](#winning-universe) and — if they have received an Initial Report — are reset back to the beginning of their first Dispute Round. The Fork is then complete and the regular 7-day Fee Window cycle continues.

There are a few important things to note about the Forking process:

First, there is no time limit on migrating tokens from the Parent Universe to a Child Universe. Tokens may be migrated after the 60-day Forking Period; they simply will not count towards the determination of the Winning Universe. To encourage greater participation during the Forking Period, all token holders who migrate their REP during the 60-day Forking Period will receive 5% additional REP in the Child Universe to which they migrated. This reward is paid for via currency inflation. 

Second, [Reporters](#reporter) that have Staked REP on one of the Forking Market’s Outcomes cannot change their position during a Fork. That is, REP that was Staked on an Outcome in the Parent Universe can be migrated only to the Child Universe that corresponds to that Outcome. For example, if a Reporter helped fulfill a successful Dispute Bond in favor of Outcome A during some previous Dispute Round, then the REP they have Staked on Outcome A can only be migrated to Universe A during a Fork.

Third, sibling Universes are entirely disjoint. REP tokens that exist in one Universe cannot be used to Report on events or earn rewards from Markets in another Universe. Only REP belonging to a Universe is valid REP to use within that Universe. Migrating REP from the Parent Universe to a Child Universe can be thought of as a permanent conversion of Parent Universe REP into Child Universe REP. Since users presumably will not want to create or trade on Markets in a Universe whose Oracle is untrustworthy, REP that exists in a Universe that does not correspond to objective reality is unlikely to earn its owner any fees, and therefore should not hold any significant market value. Thus REP tokens migrated to a Universe which doesn’t correspond to objective reality are effectively “burned” in the sense that they should hold no market value, regardless of whether or not the objectively false Universe ends up being the Winning Universe after a Fork.