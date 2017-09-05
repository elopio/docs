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

A Binary Market is a [market](#market) with only two [outcomes](#outcome), as well as Indeterminate which is always a possible [outcome](#outcome). Binary markets are for yes or no questions, if you need more than a yes or no then a [Categorical](#categorical-markets) or [Scalar](#scalar-markets) market might fit your needs better.

## Categorical Markets

A Categorical Market is a [market](#market) with more than 2 potential [outcomes](#outcome), but no more than 8. As with all markets, Indeterminate is also an [outcome](#outcome) not included in the 8 outcome maximum. Categorical Markets are best for multiple choice type questions, which team wins a tournament or what color tie the President of the United States wears at his next press conference. If you just need a yes or no question, you will probably want to make a [Binary Market](#binary-markets). If you wanted to guess the temperature in degrees on a certain day, you would probably want to use a [Scalar Market](#scalar-markets) for that, as it would be very difficult to pin down 8 possibilities and it's not a yes or no question.

## Maximum Display Price

The Maximum Display Price is the maximum price allowed for a share on a market. For [Binary](#binary-markets) or [Categorical](#categorical-markets) Markets this value is always 1, as in 1 ETH.

## Market

A market is created by users of Augur for a small fee. They are used to describe an upcoming event that people would presumably be interested in wagering on. They should also provide information on how to verify the [outcome](#outcome) of the event, the more specific the better. Each market created on the Augur network will have an automatically managed order book, which will allow users to buy and sell shares of different outcomes of the market. The [Market Creator](#market-creator) can set the trading fee for the market, which once set cannot be lowered, which will determine their cut of all shares settled on their market. There are three different market types supported by Augur, they are: [Binary](#binary-markets), [Categorical](#categorical-markets), and [Scalar](#scalar-markets).

## Market Creator

A Market Creator is simply a user who created a [market](#market). They are charged a small fee to make a new [market](#market) but can determine the trading fees for settlement of shares on that [market](#market). Market Creators are incentivized to create popular [markets](#market) so as to generate the most amount of settlement fees for themselves.

## Minimum Display Price

## Outcome

An outcome is simply a potential result of a [market](#market)'s future event. For example, a [market](#market) with a question of "Will it rain anywhere in New York City on November 1st, 2032 as reported by www.weather.com?" would have three potential outcomes: Yes, No, and Indeterminate. Indeterminate would be an option if the world blew up before November 1st, 2032 and there was no New York City or www.weather.com to verify the [market](#market). More realistically this can happen for [markets](#market) that have too vague of a question. A good example of a vague market that would most likely be voted indeterminate would be "Does God exist?" as no one has a definitive answer.

## Scalar Markets

A Scalar Market is a [market](#market) with a range for potential [outcomes](#outcome). A scalar market example might be "According to www.weather.com, what will the temperature in Fahrenheit be at SFO on January 3rd, 2062 at 1:00pm?". In this example market, we might set the [minDisplayPrice](#minimum-display-price) of the market to -50 and the [maxDisplayPrice](#maximum-display-price) to 150. This would allow for the market to settle on any number between the two. Sometimes you don't need a range of potential [outcomes](#outcome), only a simple yes/no or a small number of choices, in these cases you would want to use a [Binary](#binary-markets) or [Categorical](#categorical-markets) Market respectively.
