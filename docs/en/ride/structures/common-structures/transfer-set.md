# TransferSet (for Standard Library version 3)

> :warning: The structure is disabled in Standard library version 4. Starting with version 4 use `ScriptTransfer` directly, see the [Callable function](/en/ride/functions/callable-function) section.

Structure of a list of [token](/en/blockchain/token) transfers.

## Constructor

``` ride
TransferSet(transfers: List[ScriptTransfer])
```

## Fields

|   #   | Name | Data type | Description |
| :--- | :--- | :--- | :--- |
| 1 | transfers | [List](/en/ride/data-types/list)[[ScriptTransfer](/en/ride/structures/common-structures/script-transfer)] | List of token transfers |
