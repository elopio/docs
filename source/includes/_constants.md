API Constants
========
augur.js contains a number of constants in the `augur.constants` object, which are listed below.

### augur.constants.AUGUR_UPLOAD_BLOCK_NUMBER (string)

Description pending.

### augur.constants.BLOCKS_PER_CHUNK (number)

Description pending.

### augur.constants.CANCEL_ORDER_GAS (string)

Gas limit used when canceling an [Order](#order) on the [Order Book](#order-book).

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

### augur.constants.DEFAULT_NETWORK_ID (string)

Description pending.

### augur.constants.DEFAULT_NUM_TICKS (Object)

Description pending.

### augur.constants.DEFAULT_SCALAR_TICK_SIZE (string)

[Tick](#tick) size that is used when creating a [Scalar Market](#scalar-market) if one is not specified.

### augur.constants.ETERNAL_APPROVAL_VALUE (string)

Used when calling the `approve` function for one of Augur's ERC-20 tokens, such as Cash, ReputationToken, or ShareToken. It is equal to 2^256 - 1, which is the maximum amount tokens that can be approved for Augur to spend on behalf of a particular account.

### augur.constants.GET_LOGS_DEFAULT_FROM_BLOCK (string)

Block number at which Augur Node will begin scanning for logged events emitted from Augur's smart contracts.

### augur.constants.GET_LOGS_DEFAULT_TO_BLOCK (string)

Block number up to which Augur Node will scan for logged events emitted from Augur's smart contracts.

### augur.constants.IVSIZE (number)

Description pending.

### augur.constants.KDF (string)

Description pending.

### augur.constants.KEYSIZE (number)

Description pending.

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

### augur.constants.ROUNDS (number)

Description pending.

### augur.constants.STAKE_TOKEN_STATE (Object)

Description pending.

### augur.constants.TRADE_GAS (string)

Description pending.

### augur.constants.ZERO (BigNumber)

A BigNumber with the value of 0.