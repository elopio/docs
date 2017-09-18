
Reporting
=========
<aside class="notice">The Reporting section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

<a href="images/Reporting Diagram.png"><img src="images/Reporting Diagram.png"></a>

The Reporting Diagram above is a high level overview of [Reporting](#report) implemented by Augur. There are four ways for a [Market](#market) to attempt to [Finalize](#finalized-market): [Automated Reporting](#automated-reporting), [Limited Reporting](#limited-reporting), [All Reporting](#all-reporting), and rarely a [Fork](#fork). Markets in Augur have 10 potential states after Market Creation. The potential states of a Market are as follows:

1. Pre-Reporting
2. Automated Reporting
3. Automated Dispute
4. Awaiting Migration
5. Limited Reporting
6. Limited Dispute
7. All Reporting
8. All Dispute
9. Forking
10. Finalized

The Pre-Reporting (1) state is the time period where the market's [End Time](#end-time) has not come to pass, generally this is the most active trading period. Once the End Time has come to pass, a Market with an [Automated Reporter](#automated-reporter) set will immediately enter it's Automated Reporting state.

The [Automated Reporting](#automated-reporting) (2) state is up to three days long. During this state, the market is expected to [Reported](#report) on by the Automated Reporter at some point during this three day period. As soon as the Automated Reporter submits their Report the Market will be moved into the Automated Dispute (3) state. The Automated Dispute state lasts for three days, during which any [REP](#rep) holder can post a [Dispute Bond](#dispute-bond) to challenge the [Proposed Outcome](#proposed-outcome) submitted by the Automated Reporter. A Market in the Automated Dispute state is considered a [Market Awaiting Finalization](#market-awaiting-finalization) because if no Dispute Bond is posted the Proposed Outcome will become final and the Market will be considered [Finalized](#finalized-market) (10). If a Dispute Bond is posted during the Automated Dispute state, or if the Automated Reporter fails to report during the Automated Reporting state then the market will be moved into the Awaiting Migration (4) state.

The Awaiting Migration (4) state can last up to 30 days and theoretically could be as short as a second long. During this time the Market is waiting for the next [Reporting Window](#reporting-window) to begin so that the Market can be moved into the Limited Reporting (5) state. Reporting Windows last a total of 30 days, the first 27 days are for the [Reporting Phase](#reporting-phase), the following 3 days are for the [Dispute Phase](#dispute-phase). When a Reporting Window ends another Reporting Window immediately starts.

Once a new Reporting Window has begun, any markets in the Awaiting Migration (4) state will be moved into the Limited Reporting (5) state, and the Reporting Phase will begin. During the Reporting Phase markets in both a Limited Reporting (5) and All Reporting (7) state will be reported on by [Registered Reporters](#reporter).

Once 27 days have passed the Reporting Window's Reporting Phase ends and the Dispute Phase begins. All Markets previously in the Limited Reporting (5) or All Reporting (7) states will move into the Limited Dispute (6) and All Dispute (8) states respectively. The Proposed Outcome for these markets becomes whichever outcome had the most REP staked on it during the Reporting Phase by Reporters. During the Dispute Phase any Market in the Limited Dispute or All Dispute state can be challenged by any REP holder willing to post a Dispute Bond.

If a Market in the Limited Dispute (6) state is challenged then it will be moved into the All Reporting (7) state at the start of the next Reporting Window, which should be a maximum of three days away from the posting of the dispute bond. If a Market in the All Dispute (8) state is challenged then that market will cause a [Fork](#fork) and be moved into the Fork (9) state.

The Fork (9) state is a special and rarely entered state. A fork lasts sixty (60) days, and creates multiple [Branches](#branch) of Augur. A Branch can be thought of as a different universe of Augur where everything is the same as all the other Branches, when they are first created anyway, except for the Finalized Outcome of the Market that has entered the Fork state.

Users can migrate their REP from the original branch or universe into one of the new branches (whichever they choose). Migration is one-way and final. The universe with the most REP migrated to it by the end of the Fork period (60 days) will be chosen as the winning branch. Forked markets are considered Finalized (10) in each universe but only the winning universe will be able to [Settle](#settlement) shares and reward reporters for their work.

A Market can move into the Finalized (10) state anytime they have a Reported Proposed Outcome and move through a Dispute Phase without being challenged. Forks cannot be challenged and their markets are always considered Finalized. Once a market is finalized, traders can [Settle](#settlement) their [positions](#position) with the market.

The majority of Markets will most likely use Automated Reporting as it removes the need to wait for a Reporting Window to get to Market Settlement. Automated Reporting is designed to be fast and because of this doesn't leverage the decentralized oracle system built into Augur. Instead we are trusting the market creator's chosen Automated Reporter to accurately report on the outcome of the market. The result of Automated Reporting is still challengeable with a dispute bond so if the Automated Reporter does incorrectly report the outcome of the market it can be adjudicated by Limited Reporting in the next available Reporting Window. With the dispute system in place, we don't actually lose any security or truth as an incorrect Proposed Outcome for a market can be disputed.

Reporting Windows and Registration
----------------------------------

[Reporting Windows](#reporting-window) continually occur and last for thirty days. Reporting Windows have two phases, the Reporting Phase and the Dispute Phase. The Reporting Phase lasts for twenty seven (27) days in which Registered Reporters will be Reporting on Markets in the Limited Reporting and All Reporting states. The Dispute Phase lasts three (3) days immediately following the Reporting Phase. During a Dispute Phase any REP holder is able to post a Dispute Bond to challenge the Proposed Outcome of the Market. You do not need to have registered for the Reporting Window to post a Dispute Bond. In order to earn Reporting Fees as a REP holder you need to Report on Markets during the Reporting Phase.

REP holders who intend to Report on the outcome of markets in a Limited Reporting or All Reporting state will need to register to report prior to the Reporting Window's start time. To do this, REP holders will purchase a [Registration Token](#registration-token), which are tied to a specific upcoming Reporting Window. The purpose of a Registration Token is to allow the system to get an approximation as to how many Reporters will be participating in the Reporting Window. This is used to calculate how many Markets each Reporter needs to Report on in order for the system to get its target number of Reports per Market. Registration Tokens can be purchased as far in advanced as you would like for any future Reporting Window, but cannot be purchased for a Reporting Window in progress or a past Reporting Window.
