API Constants
========
augur.js contains a number of constants in the `augur.constants` object, which are listed below.

### augur.constants.AUGUR_UPLOAD_BLOCK_NUMBER (string)

Description pending.

### augur.constants.BLOCKS_PER_CHUNK (number)

Description pending.

### augur.constants.CANCEL_ORDER_GAS (string)

Gas limit used when canceling an [Order](#order) on the [Order Book](#order-book).

### augur.constants.CONTRACT_INTERVAL (Object)

Object that acts as an enum containing the following values: 

* CLAIM_PROCEEDS_WAIT_TIME - Number of seconds that the [Post-Finalization Waiting Period](#post-finalization-waiting-period) lasts.
* DESIGNATED_REPORTING_DURATION_SECONDS - Maximum number of seconds the [Designated Reporting Phase](#designated-reporting-phase) can last.
* DISPUTE_ROUND_DURATION_SECONDS - Maximum number of seconds a [Dispute Round](#dispute-round-phase) can last.
* FORK_DURATION_SECONDS - Maximum number of seconds a [Fork Period](#fork-period) can last.

### augur.constants.CREATE_BINARY_MARKET_GAS (string)

Gas limit used when creating a new [Binary Market](#binary-market), as a hexadecimal string.

### augur.constants.CREATE_CATEGORICAL_MARKET_GAS (string)

Gas limit used when creating a new [Categorical Market](#categorical-market), as a hexadecimal string.

### augur.constants.CREATE_ORDER_GAS (string)

Gas limit used when creating an [Order](#order).

### augur.constants.CREATE_SCALAR_MARKET_GAS (string)

Gas limit used when creating a new [Scalar Market](#scalar-market), as a hexadecimal string.

### augur.constants.DEFAULT_GASPRICE (number)

Description pending.

### augur.constants.DEFAULT_MAX_GAS (string)

Default maximum gas limit, as a hexadecimal string.

### augur.constants.DEFAULT_NETWORK_ID (string)

Description pending.

### augur.constants.DEFAULT_NUM_TICKS (Object)

Description pending.

### augur.constants.DEFAULT_SCALAR_TICK_SIZE (string)

[Tick](#tick) size that is used when creating a [Scalar Market](#scalar-market) if one is not specified.

### augur.constants.ETERNAL_APPROVAL_VALUE (string)

Used when calling the `approve` function for [Cash](#cash), which is an [ERC-20](https://en.wikipedia.org/wiki/ERC20) wrapper for ETH that Augur uses internally. It is equal to 2^256 - 1, which is the maximum amount of Cash that can be approved for Augur to spend on behalf of a particular account.

### augur.constants.GET_LOGS_DEFAULT_FROM_BLOCK (string)

Block number at which Augur Node will begin scanning for logged events emitted from Augur's smart contracts.

### augur.constants.GET_LOGS_DEFAULT_TO_BLOCK (string)

Block number up to which Augur Node will scan for logged events emitted from Augur's smart contracts.

### augur.constants.MINIMUM_TRADE_SIZE (BigNumber)

Description pending.

### augur.constants.ORDER_STATE (Object)

Contains the constants used to represent each of the states an [Order](#order) can be in.

### augur.constants.PARALLEL_LIMIT (number)

Maximum number of transactions to auto-submit in parallel.

### augur.constants.PRECISION (Object)

Description pending.

### augur.constants.REPORTING_STATE (Object)

Contains the constants used to represent each of the [Reporting](report) states a [Market](#market) can be in.

### augur.constants.STAKE_TOKEN_STATE (Object)

Description pending.

### augur.constants.TRADE_GAS (string)

Description pending.

## augur.constants.TRADE_GROUP_ID_NUM_BYTES (number)

Number of bytes that comprise a trade group ID. (Trade group IDs are used by Augur's UI to uniquely identify a trade.)

### augur.constants.ZERO (BigNumber)

A BigNumber with the value of 0.