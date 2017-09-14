
Reporting
=========
<aside class="notice">The Reporting section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates. **The Legacy Reporting and Legacy Reporting Outcomes sections below are deprecated and can be ignored.** </aside>

<a href="images/Reporting Diagram.png"><img src="images/Reporting Diagram.png"></a>

The Reporting Diagram above is a high level overview of [Reporting](#report) implemented by Augur. There are four ways for a [Market](#market) to be [Resolved](#market-resolution): [Automated Reporting](#automated-reporting), [Limited Reporting](#limited-reporting), [All Reporting](#all-reporting), and rarely a [Fork](#fork). Markets in Augur have 10 potential states after [Market Creation](#market-creation). The potential states of a Market are as follows:

1. Pre-Reporting
2. Automated Reporting
3. Automated Dispute
4. Waiting for next Reporting Window
5. Limited Reporting
6. Limited Dispute
7. All Reporting
8. All Dispute
9. Forking
10. Finalized

The Pre-Reporting (1) state is the time period where the market's [End Time](#end-time) has not come to pass and there is open trading on the various outcomes for the Market. Once the End Time has come to pass, a Market with an [Automated Reporter](#automated-reporter) set will immediately enter it's Automated Reporting state.

The [Automated Reporting](#automated-reporting) (2) state is up to three days long. During this state, the market is expected to be resolved by the Automated Reporter at some point during this three day period. As soon as the Automated Reporter submits his Report the Market will be moved into the Automated Dispute (3) state. The Automated Dispute state lasts for three days, during which any REP holder can post a [Dispute Bond](#dispute-bond) to challenge the [Winning Outcome](#winning-outcome) resolved by the Automated Reporter. If a Dispute Bond is posted during the Automated Dispute state, or if the Automated Reporter fails to report during the Automated Reporting state then the market will be moved into the Waiting for next Reporting Window (4) state.

The Waiting for next Reporting Window (4) state can last up to 30 days and theoretically could be as short as a second long. During this time the Market is essentially waiting for the next Reporting Window to begin so that the Market can be moved into the Limited Reporting (5) state. Reporting Windows last a total of 30 days, the first 27 days are for the reporting phase, the following 3 days are for the dispute phase. When a Reporting Window ends another Reporting Window immediately starts.

Once a new Reporting Window has begun, any markets in the Waiting for next Reporting Window (4) state will be moved into the Limited Reporting (5) state, and the Reporting Phase will begin. During the Reporting Phase markets in both a Limited Reporting (5) and All Reporting (7) state will be reported on by Registered Reporters.

Once 27 days have passed the Reporting Window enters the Dispute Phase and all Markets previously in the Limited Reporting (5) or All Reporting (7) states will move into the Limited Dispute (6) and All Dispute (8) states respectively. The Winning Outcome resolved for these markets are whichever outcome had the most REP wagered on it during the Reporting Phase by Reporters. During the Dispute Phase any Market in the Limited Dispute or All Dispute state can be challenged by any REP holder willing to post a Dispute Bond.

If a Market in the Limited Dispute (6) state is challenged then it will be moved into the All Reporting (7) state at the start of the next Reporting Window, which should be a maximum of three days away from the posting of the dispute bond. If a Market in the All Dispute (8) state is challenged then that market will cause a [Fork](#fork) and be moved into the Fork (9) state.



Legacy Reporting
----------
<aside class="warning">The Legacy Reporting section is deprecated. This information is no longer accurate as the reporting system has undergone some changes. This document will be updated prior to release of Augur.</aside>

<a href="images/reporting_cycle.svg"><img src="images/reporting_cycle.svg" onerror="this.src='images/reporting_cycle.png'"></a>

This diagram shows the Reporting cycle for an event (and its associated market) which was created during some previous cycle (off the diagram to the left) and which expires sometime during the cycle marked as "Reporting cycle 1".  Each cycle takes 60 days to complete.  Including the steps needed to fully complete all payouts and Reputation redistribution, resolving an event takes three full Reporting cycles.

<aside class="notice">In Augur, the terms "Reporting period" and "Reporting cycle" are used interchangeably throughout the codebase.</aside>

Legacy Reporting outcomes
------------------

### Yes or no (binary)
- no: `2**64`
- yes: `2*2**64`
- indeterminate: `3*2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`

### Multiple choice (categorical)

- min: `1`
- max: `2**64`
- indeterminate: `2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`

### Numerical (scalar)
- min: `1`
- max: `2**64`
- indeterminate: `2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`
