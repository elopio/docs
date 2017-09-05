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

## Binary Markets

A Binary Market is a [market](#market) with only two [outcomes](#outcome), as well as Indeterminate which is always a possible outcome. Binary markets are for yes or no questions, if you need more than a yes or no then a [Categorical](#categorical-markets) or [Scalar](#scalar-markets) market might fit your needs better.

## Categorical Markets

A Categorical Market is a [market](#market) with more than 2 potential [outcomes](#outcome), but no more than 8. As with all markets, Indeterminate is also an outcome not included in the 8 outcome maximum. Categorical Markets are best for multiple choice type questions, which team wins a tournament or what color tie the President of the United States wears at his next press conference. If you just need a yes or no question, you will probably want to make a [Binary Market](#binary-markets). If you wanted to guess the temperature in degrees on a certain day, you would probably want to use a [Scalar Market](#scalar-markets) for that, as it would be very difficult to pin down 8 possibilities and it's not a yes or no question.

## Complete Sets

A Complete Set is a complete collection of whole shares in every outcome owned by one address. When a user amasses a complete set, the shares are destroyed and the range of the [market](#market) ([maxDisplayPrice](#maximum-display-price) - [minDisplayPrice](#minimum-display-price)) - [Trading Fees](#trading-fees) ETH will be paid out to the owner of the complete set.

## End Time

End Time is the date and time that a [market](#market)'s event will have come to pass and should be known. After this date and time has passed the market will get reported on and finalized.

## Market

A market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each market created on the Augur network will have an automatically managed order book, which will allow users to buy and sell shares of different outcomes of the market. The [Market Creator](#market-creator) can set the [Trading Fee](#trading-fee) for the market, which once set cannot be lowered, which will determine their cut of all shares settled on their market. There are three different market types supported by Augur, they are: [Binary](#binary-markets), [Categorical](#categorical-markets), and [Scalar](#scalar-markets).

## Market Creator

A Market Creator is simply a user who created a [market](#market). They are charged a small fee to make a new market but can determine the [Trading Fee](#trading-fee) for settlement of shares on that market. Market Creators are incentivized to create popular markets so as to generate the most amount of settlement fees for themselves. Other information a market requires is the [End Time](#end-time) and a [Topic](#topic).

## Maximum Display Price

The Maximum Display Price (often seen as `maxDisplayPrice`) is the maximum price allowed for a share on a [market](#market). For [Binary](#binary-markets) or [Categorical](#categorical-markets) Markets this value is always 1, as in 1 ETH. [Scalar](#scalar-markets) markets' Maximum Display Price would be the top end of the range set by the [Market Creator](#market-creator).

## Minimum Display Price

The Minimum Display Price (often seen as `minDisplayPrice`) is the minimum price allowed for a share on a [market](#market). For [Binary](#binary-markets) or [Categorical](#categorical-markets) Markets this value is always 0, as in 0 ETH. [Scalar](#scalar-markets) markets' Minimum Display Price would be the bottom end of the range set by the [Market Creator](#market-creator).

## Outcome

An outcome is a potential result of a [Market](#market)'s future event. For example, a market with a question of "Will it rain anywhere in New York City on November 1st, 2032 as reported by www.weather.com?" would have three potential outcomes: Yes, No, and Indeterminate. Indeterminate would be an option if the world blew up before November 1st, 2032 and there was no New York City or www.weather.com to verify the market. More realistically this can happen for markets that have too vague of a question. A good example of a vague market that would most likely be voted indeterminate would be "Does God exist?" as no one has a definitive answer.

## REP

REP, also known as Reputation, Reputation Tokens, or REP Tokens, is the currency used by the Augur Decentralized Oracle System. REP is used to purchase [RegistrationTokens](#registration-tokens) for upcoming Reporting Windows and to report on the [outcome](#outcome) of [markets](#market). Once you have registered for a Reporting Window, when the time comes to report you will be shown markets that need to be finalized. You will be asked to wager REP on an outcome based on how confident you are in that outcome being the result of the question asked in the market. The markets you will see for reporting are all past their [End Time](#end-time) and the outcome should be determinable. If the outcome is not determinable you can wager your rep into the Indeterminate outcome. The more REP you wager, the larger the share of the [Reporting Fees](#reporting-fee) you will receive if you report with the consensus.

## Reporting Fee

The Reporting Fee is used to help pay for the decentralized oracle system. When shares are settled (aka destroyed), before paying out to the share holders Augur will extract the [Trading Fees](#trading-fees), which includes the [Trading Fee](#trading-fee) and The Reporting Fee. The Reporting Fees are sent to the Reporting Window that contains the [market](#market) being traded on, and are later used to pay [REP](#rep) holders for reporting on the outcome of markets.

## Scalar Markets

A Scalar Market is a [Market](#market) with a range for potential [outcomes](#outcome). A scalar market example might be "According to www.weather.com, what will the temperature in Fahrenheit be at SFO on January 3rd, 2062 at 1:00pm?". In this example market, we might set the [minDisplayPrice](#minimum-display-price) of the market to -50 and the [maxDisplayPrice](#maximum-display-price) to 150. This would allow for the market to settle on any number between the two. Sometimes you don't need a range of potential outcomes, only a simple yes/no or a small number of choices, in these cases you would want to use a [Binary](#binary-markets) or [Categorical](#categorical-markets) Market respectively.

## Topic

A Topic is a keyword used to categorize [markets](#market). All markets must have a topic, and are optionally allowed up to two sub-topics to further categorize the market. An example market for "Will the New York Giants win Super Bowl 100?" might have a Topic of "Sports" and sub-topics of "American Football" and "NFL". The Topics are set by the [Market Creator](#market-creator) when a new market is made and cannot be changed.

## Trading Fee

A Trading Fee is set by the [Market Creator](#market-creator) when he or she creates a new [Market](#market). Once the trading fee is set, it can never be increased only decreased. The Trading Fee must be between 0% and 50%. The Trading Fee and the [Reporting Fee](#reporting-fee) are both extracted at the same time whenever shares are settled on a market. Shares can be settled when a user amasses a [Complete Set](#complete-sets) or when the market has been finalized and you want to close your open position. The Trading Fee is designed to incentivize users to make popular markets as they stand to earn money if enough people trade on the market. They can then recoup their market creation cost and ideally turn a profit on posting interesting markets. The [Trading Fees](#trading-fees) are discussed in more details in the [Trading](#trading) section of the documentation.
