# SmartRaiden’s API Documentation

## Introduction
SmartRaiden has a Restful API with URL endpoints corresponding to user-facing interaction allowed by a SmartRaiden node. The endpoints accept and return JSON encoded objects. The api url path always contains the api version in order to differentiate queries to different API versions. All queries start with:  `/api/<version>/`.
## JSON Object Encoding
The objects that are sent to and received from the API are JSON-encoded. Following are the common objects used in the API.
### Channel Object
```json
{
        "channel_address": "0xc4327c664D9c47230Be07436980Ea633cA3265e4",
        "partner_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
        "balance": 200,
        "partner_balance": 100,
        "locked_amount": 0,
        "partner_locked_amount": 0,
        "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
        "state": "opened",
        "settle_timeout": 100,
        "reveal_timeout": 3
    }
```
A channel object consists of a:
-   `channel_address`  should be a  `string`  containing the hexadecimal address of the channel
 -   `partner_address`  should be a  `string`  containing the hexadecimal address of the partner with whom we have opened a channel
 - `balance` should be an integer of the amount of the `token_address` token we have available for transferring
 - `partner_balance` should be an integer of the amount of the `token_address` token partner have available for transferring
 - `locked_amount` should be an integer of the amount of the `token_address` token we have locked amount
 - `partner_locked_amount` should be an integer of the amount of the `token_address` token partner have locked amount
 - `token_address` should be a `string` containing the hexadecimal address of the token we are trading in the channel
 -   `state`  should be the current state of the channel represented by a string. Possible value are: -  `opened`: The channel is open and tokens are tradeable -  `closed`: The channel has been closed by a participant -  `settled`: The channel has been closed by a participant and also settled
 -  `settle_timeout`: The number of blocks that are required to be mined from the time that  `close()`  is called until the channel can be settled with a call to  `settle()`
 - `reveal_timeout`: The maximum number of blocks allowed between the setting of a hashlock and the revealing of the related secret
## Endpoints
Following are the available API endpoints with which you can interact with SmartRaiden.
### Querying Information About Your SmartRaiden Node
**`GET /api/<version>/address`**
Query your address. When SmartRaiden starts, you choose an ethereum/Spectrum address which will also be your SmartRaiden address
**Example Request**:
`GET http://localhost:5001/api/1/address`
**Example Response**:
*`200 OK`* and 
```json
{
    "our_address": "0x69C5621db8093ee9a26cc2e253f929316E6E5b92"
}
```
### Deploying
**`PUT/api/<version>/tokens/<token_address>`**
Registers a token. If a token is not registered yet (i.e.: A token network for that token does not exist in the registry), we need to register it by deploying a token network contract for that token.
**Example Request**:
`PUT http://localhost:5001/api/1/tokens/0xB0159439B496b8cebd54f232Ae06d61d0bE1Fe45`
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_manager_address": "0x0aa88934bc3B0E9623d9555ceA48ab60FF3f2869"
}
```
Status Codes:
* `200 Created`– A token network for the token has been successfully created
* `409 Conflict` – The token was already registered before or  The registering transaction failed.

Response JSON Object:
- **channel_manager_address** Channel management contract address

### Querying Information About Channels and Tokens
**`GET/api/<version>/channels`**
Querying all channels
**Example Request**:
`GET http://localhost:5004/api/1/channels`
**Example Response**:
*`200 OK`* and 
```json
 {
        "channel_address": "0xd5CF2248292e75531d314B118a0390132bc7a5F0",
        "partner_address": "0x088da4d932A716946B3542A10a7E84edc98F72d8",
        "balance": 100,
        "partner_balance": 100,
        "locked_amount": 0,
        "partner_locked_amount": 0,
        "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
        "state": "opened",
        "settle_timeout": 100,
        "reveal_timeout": 3
    },
    {
        "channel_address": "0xdF474bBc5802bFadc4A25cf46ad9a06589D5AF7D",
        "partner_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
        "balance": 100,
        "partner_balance": 200,
        "locked_amount": 0,
        "partner_locked_amount": 0,
        "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
        "state": "opened",
        "settle_timeout": 100,
        "reveal_timeout": 3
    }
```
Status Codes:
* `200 OK`-Successful query

Querying a specific channel
**Example Request**:
`GET http://localhost:5004/api/1/channels/0xd5CF2248292e75531d314B118a0390132bc7a5F0`
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_address": "0xd5CF2248292e75531d314B118a0390132bc7a5F0",
    "partner_address": "0x088da4d932A716946B3542A10a7E84edc98F72d8",
    "balance": 100,
    "patner_balance": 100,
    "locked_amount": 0,
    "partner_locked_amount": 0,
    "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
    "state": "opened",
    "settle_timeout": 100,
    "reveal_timeout": 0,
    "ClosedBlock": 0,
    "SettledBlock": 0,
    "OurUnkownSecretLocks": {},
    "OurKnownSecretLocks": {},
    "PartnerUnkownSecretLocks": {},
    "PartnerKnownSecretLocks": {},
    "OurLeaves": null,
    "PartnerLeaves": null,
    "OurBalanceProof": null,
    "PartnerBalanceProof": null,
    "Signature": null
}
```
Status Codes:
* `200 OK`-Successful query
* `404 Not Found` -If the channel does not exist

Querying all registered Tokens,Returns  a list of addresses of all registered tokens.
**Example Request**:
`GET http://localhost:5004/api/1/tokens`
**Example Response**:
*`200 OK`* and 
```json
[
    "0xb0159439b496b8cebd54f232ae06d61d0be1fe45",
    "0x541eefe890a10d27d947190ea976cb6dcbba650f",
    "0xf3db2928689cdbd9938d1e1ffc2c4980a96f299e",
    "0x745d52e50cd1b19563d3a3b7b6d2eb60b17e6bae"
]
```
Status Codes:
* `200 OK`-Successful query
* `404 Not Found` -If the token does not exist


 Querying all partners for a Token,Returns a list of all partners with whom you have non-settled channels for a certain token.
 **Example Request**:
 `GET http://localhost:5004/api/1/tokens/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/partners`
 **Example Response**:
*`200 OK`* and 
```json
[
    {
        "partner_address": "0x088da4d932A716946B3542A10a7E84edc98F72d8",
        "channel": "api/1/channles/0xd5CF2248292e75531d314B118a0390132bc7a5F0"
    },
    {
        "partner_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
        "channel": "api/1/channles/0xdF474bBc5802bFadc4A25cf46ad9a06589D5AF7D"
    }
]
```
Status Codes:
* `200 OK`-Successful query
* `404 Not Found` -If the token does not exist

Response JSON Array of Objects:
-   **partner_address**  (_address_) – The partner we have a channel with
-   **channel**  (_link_) – A link to the channel resource
### Token Swaps
**`PUT /api/<version>/token_swaps/<target_address>/<identifier>`**

You can perform a token swap by using the  `token_swaps`  endpoint. A swap consists of two users agreeing on atomically exchanging two different tokens at a particular exchange rate.
tips：
* The parties involved in Swaps have an effective channel
* Call *taker* first and then call *maker*

**Example Request**:
*the taker:*
`PUT http://localhost:5001/api/1/token_swaps/0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd/3333`
with payload:
```json
{
    "role": "taker",
    "sending_amount": 50,
    "sending_token": "0x745d52e50cd1b19563d3a3b7b6d2eb60b17e6bae",
    "receiving_amount": 5,
    "receiving_token": "0x541eefe890a10d27d947190ea976cb6dcbba650f"
}
```
*the maker:*
`PUT http:// localhost:5002/api/1/token_swaps/0x69C5621db8093ee9a26cc2e253f929316E6E5b92/3333`
with payload:
```json
{
    "role": "maker",
    "sending_amount": 5,
    "sending_token": "0x541eefe890a10d27d947190ea976cb6dcbba650f",
    "receiving_amount": 50,
    "receiving_token": "0x745d52e50cd1b19563d3a3b7b6d2eb60b17e6bae"
}
```
Status Codes:
* `201 Created`-Successful query
* `400  Bad Request` -no available route

### Channel Management
**`PUT/api/<version>/channels`**
Opens channel.
**Example Request**:
`PUT http:// localhost:5001/api/1/channels`
with payload:
```json
{
    "partner_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
    "token_address": "0x541eefe890a10d27d947190ea976cb6dcbba650f",
    "balance": 200,
    "settle_timeout": 100
}
```
The  `balance`  field will signify the initial deposit you wish to make to the channel.

The request to the endpoint should later return the fully created channel object from which we can find the address of the channel.
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_address": "0x7f9bc53F7b3e08a3A9De564740f7FAf9Decb16B9",
    "partner_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
    "balance": 200,
    "partner_balance": 0,
    "locked_amount": 0,
    "partner_locked_amount": 0,
    "token_address": "0x541eeFe890A10D27d947190EA976CB6DCBba650f",
    "state": "opened",
    "settle_timeout": 100,
    "reveal_timeout": 0
}
```
Status Codes:
* `200 OK`-Channel created successfully
* `404 Not Found` -If the token does not exist
* `409  Conflict` -NewChannel tx execution failed

**`PATCH/api/<version>/channels/<channel_address>`**
 Close Channel
**Example Request**:
`PATCH http:// localhost:5001/api/1/channels/0xD955A1BA24058BFbFfD98dF78253a861e5B029b9`
with payload:
```json
{"state":"closed"}
```
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_address": "0xD955A1BA24058BFbFfD98dF78253a861e5B029b9",
    "partner_address": "0x1DdaC67E610c22d19e887FB1937bEe3079B56CD1",
    "balance": 200,
    "partner_balance": 0,
    "locked_amount": 0,
    "partner_locked_amount": 0,
    "token_address": "0x541eeFe890A10D27d947190EA976CB6DCBba650f",
    "state": "closed",
    "settle_timeout": 100,
    "reveal_timeout": 0
}
```
Settle Channel
**Example Request**:
`PATCH http:// localhost:5001/api/1/channels/0xD955A1BA24058BFbFfD98dF78253a861e5B029b9`
with payload:
```json
{"state":"settled"}
```
**Example Response**:
*`200 OK`* and 
```json
```
**`PATCH  /api/<version>/channels/<channel_address>`**
 Deposit to a Channel
 You can deposit more of a particular token to a channel by updating the `balance` field of the channel in the corresponding endpoint with a `PATCH` http request.
 **Example Request**:
 `PATCH http://localhost:5002/api/1/channels/0x7f9bc53F7b3e08a3A9De564740f7FAf9Decb16B9`
 with payload:
```json
{
    "balance": 100
}
```
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_address": "0x7f9bc53F7b3e08a3A9De564740f7FAf9Decb16B9",
    "partner_address": "0x69C5621db8093ee9a26cc2e253f929316E6E5b92",
    "balance": 100,
    "partner_balance": 200,
    "locked_amount": 0,
    "partner_locked_amount": 0,
    "token_address": "0x541eeFe890A10D27d947190EA976CB6DCBba650f",
    "state": "opened",
    "settle_timeout": 100,
    "reveal_timeout": 0
}
```
Status Codes:
* `200 OK`-For successful Deposit
* `400 Bad Request` -If the provided json is in some way malformed
### Connection Management

**`GET  /api/<version>/connections`**
 Querying connections details
You can query for details of previously joined token networks by making a GET request to the connection endpoint.
 **Example Request**:
 `GET http:// localhost:5003/api/1/connections`
 **Example Response**:
*`200 OK`* and 
```json
{
    "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE": {
        "funds": 0,
        "sum_deposits": 200,
        "channels": 1
    }
}
```
**Example Response**:
*`200 OK`* and 
```json
{
    "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE": {
        "funds": 0,
        "sum_deposits": 280,
        "channels": 3
    }
}
```
Response JSON Array of Objects:
-   **funds**  (_int_) – Funds from last connect request
-   **sum_deposits**  (_int_) – Sum of deposits of all currently open channels
-   **channels**  (_int_) – Number of channels currently open for that token 

Status Codes:
* `200 OK`-For a successful query

**`PUT  /api/<version>/connections/<token_address>`**
Automatically join a token network. The request will only return once all blockchain calls for opening and/or depositing to a channel have completed.
 **Example Request**:
 `PUT http://localhost:5001/api/1/connections/0xf1b0964f1e19ecf07ddd3bd8e20138c82680395d`
 **Example Response**:
*`201 Created`* 
Status Codes:
* `201 Created`-For a successful connection creation
* `500  Internal Server Error`-Internal SmartRaiden node error

**`DELETE  /api/<version>/connections/<token_address>`**
The request will only return once all blockchain calls for closing/settling a channel have completed.

Important note. If no arguments are given then raiden will only close and settle channels where your node has received transfers. This is safe from an accounting point of view since deposits can’t be lost and provides for the fastest and cheapest way to leave a token network when you want to shut down your node.

If the default behaviour is not desired and the goal is to leave all channels irrespective of having received transfers or not then you should provide as payload to the request  `only_receiving_channels=false`

A list with the addresses of all the closed channels will be returned.
 **Example Request**:
 `DELETE http://localhost:5003/api/1/connections/0x541eefe890a10d27d947190ea976cb6dcbba650f`
  with payload:
 ```js
 {
  "only_receiving_channels":false
}
```
 **Example Response**:
*`200 OK`* and 
```js
[
    "0x08Bb272f51c8974ACe71648d01afE933384A762e",
    "0x68f9390554789c2D658540C1d3A450fb858a849e",
    "0xc0cd666a125F9bbf4dAeFcbd24F2dc26f7BC9f8D"
]
```
The response is a list with the addresses of all closed channels.

Request JSON Object:

-   **only_receiving_channels**  (_boolean_) – Only close and settle channels where your node has received transfers. Defaults to  `true`.

Status Codes:
- `200 OK`-For successfully leaving a token network
- `500  Internal Server Error`-Internal SmartRaiden node error
### Transfers
**`POST  /api/<version>/transfers/<token_address>/<target_address>`**

 Initiating a Transfer
 You can create a new transfer by making a  `POST`  request to the following endpoint along with a json payload containing the transfer details such as amount and identifier. Identifier is optional.
 
The request will only return once the transfer either succeeded or failed. A transfer can fail due to the expiration of a lock, the target being offline, channels on the path to the target not having enough `settle_timeout` and `reveal_timeout` in order to allow the transfer to be propagated safely e.t.c
 **Example Request**:
 `POST http://localhost:5002/api/1/transfers/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/0x69C5621db8093ee9a26cc2e253f929316E6E5b92`
with payload:
```json
{
    "amount":10,
    "fee":0,
    "is_direct":false
}
```
 **Example Response**:
*`200 OK`* and 
```json
{
    "initiator_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
    "target_address": "0x69C5621db8093ee9a26cc2e253f929316E6E5b92",
    "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
    "amount": 10,
    "identifier": 5018140839335492878,
    "fee": 0,
    "is_direct": false
}
```
Request JSON Object:

-   **amount**  (_int_) – Amount to be transferred
-   **fee**  (_int_) –  incentivize nodes to retain more balance in payment channels via a method to take a charge for them(default:0)
- **is_direct"**(_boolean_)–  If it is set to true, it can only satisfy the two parties who have direct access to the transaction. If the two sides do not have direct access, they will give up the transaction.

Status Codes:
- `200 OK` – Successful transfer
- `409 Conflict`– If the address or the amount is invalid or if there is no path to the target
-  `500  Internal Server Error`-Internal SmartRaiden node error
### Querying Events

Events are kept by the node. Once an event endpoint is queried the relevant events from either the beginning of time or the given block are returned.

Events are queried by two different endpoints depending on whether they are related to a specific channel or not.

All events can be filtered down by providing the query string argument  `from_block`  to signify the block from which you would like the events to be returned.
**`GET  /api/<version>/events/network`**
Query for registry network events.
 **Example Request**:
 `GET http://localhost:5001/api/1/events/network`
 **Example Response**:
*`200 OK`* and 
```json
[
    {
        "event_type": "TokenAdded",
        "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
        "channel_manager_address": "0x48fA4f2230DB0dEEA3989014CD21857DF6210B33"
    },
    {
        "event_type": "TokenAdded",
        "token_address": "0x541eeFe890A10D27d947190EA976CB6DCBba650f",
        "channel_manager_address": "0xdC47DF3eAc0E9a8373A258ccf4838bE3540a9D4E"
    },
    {
        "event_type": "TokenAdded",
        "token_address": "0xF3DB2928689Cdbd9938d1e1Ffc2c4980a96f299E",
        "channel_manager_address": "0x78494a9F7278F5eE5a3faB445685EABA7add547a"
    },
    {
        "event_type": "TokenAdded",
        "token_address": "0xB0159439B496b8cebd54f232Ae06d61d0bE1Fe45",
        "channel_manager_address": "0x0aa88934bc3B0E9623d9555ceA48ab60FF3f2869"
    }
]
```
Status Codes:
- `200 OK` – For successful Query
- `404  Not Found`–If the provided query string is malformed

**`GET  /api/<version>/events/tokens/<token_address>`**
Querying token network events
 **Example Request**:
 `GET http://localhost:5001/api/1/events/tokens/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE`
**Example Response**:
*`200 OK`* and 
```json
[
    {
        "event_type": "ChannelNew",
        "settle_timeout": 40,
        "netting_channel": "0x5629954B107E1889516E0CEC046432aa20f70778",
        "participant1": "0x69C5621db8093ee9a26cc2e253f929316E6E5b92",
        "participant2": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
        "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE"
    },
    {
        "event_type": "ChannelNew",
        "settle_timeout": 100,
        "netting_channel": "0x8898917d1d2DF53595DA560f5b162BC7e6BCBDa0",
        "participant1": "0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5",
        "participant2": "0x62e68b5c745Fa25B439BF613dC1Fcd262277fa93",
        "token_address": "0x745D52e50cd1b1
        9563D3a3B7B6d2eB60b17E6bAE"
    }
]
```
Status Codes:
- `200 OK` – For successful Query
- `404  Not Found`–If the provided query string is malformed

**`GET  /api/<version>/events/channels/<channel_registry_address>`**
 Querying channel events
  **Example Request**:
  `GET http://localhost:5002/api/1/events/channels/0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA`
  **Example Response**:
*`200 OK`* and 
```json
[
    {
        "balance": 100,
        "block_number": 2469154,
        "event_type": "ChannelNewBalance",
        "participant": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd"
    },
    {
        "balance": 100,
        "block_number": 2727927,
        "event_type": "ChannelNewBalance",
        "participant": "0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5"
    },
    {
        "amount": 10,
        "block_number": 3417198,
        "event_type": "EventTransferSentSuccess",
        "identifier": 5018140839335492878,
        "target": "0x69c5621db8093ee9a26cc2e253f929316e6e5b92"
    }
]
```
Status Codes:
- `200 OK` – For successful Query
- `400  Bad Request`–If the channel does not exist

### SmartRaiden Monitoring
SmartRaiden Monitoring (also known as SM), is a third-party delegation service aiming to facilitate mobile devices to autonomously enforce on-chain services like UpdateTransfer & WithDraw according to predetermined delegation, when they're offline.

SmartRaiden Monitoring primarily interacts with three parts : Application, SmartRaiden and the Public Chain. 
***

### Start up SM 
### 1. Preparation 
To start up a smartraiden monitoring, one need create at least 4 nodes. One of them is to charge fees for monitoring service, one to play as a monitoring node, and another two nodes to make transactions. 

For instance : 
* node 1 `0x69c5621db8093ee9a26cc2e253f929316e6e5b92`
* node 2 `0x31ddac67e610c22d19e887fb1937bee3079b56cd`
* node 3 `0xf0f6e53d6bbb9debf35da6531ec9f1141cd549d5`
* node 4 `0x6b9e4d89ee3828e7a477ea9aa7b62810260e27e9`

node 1 : charge node
node 2 : sender node
node 3 : receiver node 
node 4 : monitoring node

Check up the balances of node 2 and node 3, in case that later we can verify whether our smartraiden monitoring operates successfully, and whether balances of nodes alter.

**`GET: /api/<version>/debug/balance/<token>/<address>`**

Check-up Balance

 **Example Request**:
 `GET:http://localhost:5002/api/1/debug/balance/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5`

   **Example Response**:
*`200 OK`* and 
```js
4999795
```

 **Example Request**:
  `GET:http://localhost:5002/api/1/debug/balance/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd`

   **Example Response**: *`200 OK`* and 
```js
4989425
```

Hence, there is the current state of node 2 and node 3.
node 2 balance ：4989425
node 3 balance ：4999795

### 2. Install scripts of SM nodes

**Install SmartRaiden Monitoring :**
```
go get github.com/SmartMeshFoundation/SmartRaiden-Monitoring
cd cmd/smartraidenmonitoring
go install
```

**Install charge node :**
```sh
#!/bin/sh
#rm -rf .smartraiden
#echo privnet
#echo install ...
#go install
#set-title 1·
echo run smartraiden...
smartraiden --datadir=~/niexin/smartraiden --api-address=0.0.0.0:5001 --listen-address=127.0.0.1:40001 --address="0x69c5621db8093ee9a26cc2e253f929316e6e5b92" --keystore-path ~/.ethereum/keystore --registry-contract-address 0xA5a0E448ded405d86291D37A5561e91F72601751 --password-file 123  --eth-rpc-endpoint ws://127.0.0.1:5555  --conditionquit "{\"QuitEvent\":\"1EventSendMediatedTransferAfter\"}"  --debug --debugcrash   --verbosity 5  --ignore-mediatednode-request
#--enable-health-check  #--signal-server 182.254.155.208:5222
echo "quit ok"
```

Node 1 is necessary to add `ignore-mediatednode-request`, so that it would not work as a route node for raiden, preventing cases like smartraiden monitoring nodes are charged mistaknly.

**Install smartraiden monitoring node :** 
```sh
#!/bin/sh
echo run smartraiden-monitoring
smartraidenmonitoring  --datadir=/home/niexin/niexin/.smartraidenmonitoring --eth-rpc-endpoint ws://127.0.0.1:5555  --address="0x6b9e4d89ee3828e7a477ea9aa7b62810260e27e9" --keystore-path ~/.ethereum/keystore --registry-contract-address 0xA5a0E448ded405d86291D37A5561e91F72601751  --password-file 123 --verbosity 5  --debug   --smt 0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE
echo "quit ok"
```
`--smt` : tokens to be transferred.
***

### Procedures for Test
#### 1. Node 2 sends 20 tokens to node 3

Via API offered below 
**`POST  /api/<version>/transfers/<token_address>/<target_address>`**


**Example Request**:
`
http://{{ip2}}/api/1/transfers/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5
`

with payload:
```json
{
    "amount":100,
    "fee":0,
    "is_direct":true
}
```

   **Example Response**:
*`200 OK`* and 
```json
{
    "initiator_address": "0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd",
    "target_address": "0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5",
    "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
    "amount": 100,
    "identifier": 13450803429907647519,
    "fee": 0,
    "is_direct": true
}
```

#### 2. Check Delegation Messages
Node 3 checks any message from other nodes who require SM monitoring service.

**`GET: /api/<version1>/thirdparty/<channel_address>/<thirdparty_address>`**

**Example Request**:
`GET:http:// localhost:5003/api/1/thirdparty/0x05c468707fBdf56f944d6292fa16234167c704f0/0x6b9e4d89ee3828e7a477ea9aa7b62810260e27e9`

**Example Response**:
*`200 OK`* and 
```js
{
    "channel_address": "0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA",
    "update_transfer": {
        "nonce": 1,
        "transfer_amount": 100,
        "locksroot": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "extra_hash": "0x8cb02ef8009377267b03aaa1793cbc3c6b2fbb06f58f59bf2ee919108999f192",
        "closing_signature": "a664e8c5f09d32700e2f0110a4aa230c7d3af5a62fa86b01f4e0210c53a7627676e1d4317f39642fcb755ab469ccf43f2d0f90c3decc724d3f37aa7ba43e400b1c",
        "non_closing_signature": "abf712eb853f3b0016ee3af14c5902e0ff72efca03299b10bf50b4b2cda763993033c934fbd01f460ced62ba748b3df75de6ae6be1d07ae99a869b59f35bc84d1c"
    },
    "withdraws": null
}
```
#### 3.Commit Proofs of Delegation to SM

Commit proofs of delegation to smartraiden monitoring nodes :

**`POST: http://192.168.124.13:6000/delegate/<address_of_delegator>`**
> comment : `192.168.124.13:6000` is a smartraiden monitoring port (default to be local 6000 port).

**Example Request**:
`POST:http://192.168.124.13:6000/delegate/0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5`

with payload:
```json
{
    "channel_address": "0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA",
    "update_transfer": {
        "nonce": 2,
        "transfer_amount": 100,
        "locksroot": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "extra_hash": "0x72173e7a2c119683375e2f78037f9dcf6eaeefb518260abb7bb96edbea256eb1",
        "closing_signature": "f118f7d1ae1db9048c42b1897f7862676143269873ca9f6d9439fde9fdf3747404f3a0cb2a8913d9cebed04604c923726c9b9074fc880a746bcc54561f2cbc591b",
        "non_closing_signature": "281eb8f59a48759b0090c3e76bb88bae70ed18a2dd707de9504483a04eb08fc128e45bbf0dfc2323b4ca92177e501d16af8cb9332ca3ac3d2060a4fe34cbdf331b"
    },
    "withdraws": null
}
```

  **Example Response**:
  *`200 OK`* and 
  ```json
  {
    "Status": 3,
    "Error": ""
}
```
Status tag has 3 values : 
- status = 1 : delegation failure
- status = 2 : delegation success but lack of fund
- status = 3 : delegation success with sufficient fund

#### 4. Verify whether charge node has sufficient fund for node 3 to operate. 

To check whether delegator node (node 3) deposits enough tokens in charge node (node 1) :

**`GET: http://192.168.124.13:6000/fee/<委托方地址>`**

**Example Request**:
`GET http://192.168.124.13:6000/fee/0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5`

**Example Response**:
*`200 OK`* and 
```JSON
{
    "Available": 50,
    "NeedSmt": 3
}
```

#### 5. Node 3 disconnect 

#### 6. Node 2 close up payment channel.

**Example Request**:
` PATCH http:// localhost:5002/api/1/channels/0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA`

with payload
```js
{"state":"closed"}
```
**Example Response**:
*`200 OK`* and 
```json
{
    "channel_address": "0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA",
    "partner_address": "0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5",
    "balance": 0,
    "partner_balance": 200,
    "locked_amount": 0,
    "partner_locked_amount": 0,
    "token_address": "0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE",
    "state": "closed",
    "settle_timeout": 100,
    "reveal_timeout": 0
}
```

#### 7. Check on-chain account balance

`http://localhost:5002/api/1/debug/balance/0x745D52e50cd1b19563D3a3B7B6d2eB60b17E6bAE/0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5`
```json
4999795
```
We can conclude that node 3 can submit its delegation message, and when it goes offline, then node 4, as a third-party service node, will autonomously enforce UpdateTransfer & WithDraw. After node 2, as the counterpart of node 3, closes payment channels and settles,
node 3 could safely get all the refund in this channel.
### Fixed charges

We can incentivize nodes to retain more balance in payment channels via a method to take a charge for them, in the end to maximize the rate of successful transactions. Hence, charging fees to nodes is highly relevant to the route choosing. Right now, smartraiden adopts a route algorithm that is based on the sender node, in which sender and routers individually enforce this algorithm. Sender has no idea either which route will finally connect to the receiver node, or how many gas he will be charged, so to avoid that, our sender need to deposit extra gas in the channel. To be clear, in smartraiden 3.0 version we make every node in the network to charge the same gas, in which case it is quite easy to calculate gases spent in each transaction. This is NOT the most efficient way for each node is able to charge differently. In future version, we plan to keep a record of charge list for each node in the server, and choose a route that only requires a minimum amount of gases to connect to the receiver node, and maximizes the benefit for all the nodes in this route.
### Internet-free Payment
Internet-free Payment : Internet-free transaction is a off-chain transaction that has no necessity for Internet to be enforced. However, if there is no connection to public chain for quite a long period, we can not ensure Smartraiden is secure when transferring tokens.  So we need somehow find a method, like App, to keep the record of period of how long nodes are off-line from public chain, before any Internet-free transaction and any node receiving a transaction message under such circumstances to check whether such a transaction is in safe state.

Let’s still take Alice & Bob as our example.  When Alice proposes to give 30 tokens to Bob,  they need to create a bilateral channel  with the condition of Internet. Assume that Alice creates a direct payment channel to Bob in the spectrum, and deposits 100 tokens of hers, then in such channel, Alice has her total balance of 100 tokens, as to Bob, it’s 0 token. With Internet connection, actually they are able to make their transaction out of chain. Suppose it is the case, if Alice is transferring her part of deposit to Bob when there is something wrong with Internet connection,  and there is a meshbox near this payment channel, and both Alice and Bob have registered on this meshbox, such as :

Alice :` { ‘address’ :  “0x1a9ec3b0b807464e6d3398a59d6b0a369bf422fa”, ‘ip_port’ : “192.168.0.5:40001”}`

Bob :`{ ‘address’ : “0x8c1b2E9e838e2Bf510eC7Ff49CC607b718Ce8401”,
‘ip_port’ : “192.168.0.7:40001”}`

then the payment channel between Alice & Bob is still effective.

Right now, Alice can invoke SwitchNetwork to alter to Internet-free state, and with resort to meshbox, Alice can give her 30 tokens directly to Bob. While Internet-free state has its limitation, that is, it does not support any indirect transaction with regard to sending, transferring, and receiving. Besides, nodes can only create direct channel to do transactions, in Internet-free state.
### Node synchronization state description
It is quite significant for transaction security to keep states of participants synchronized. At the time Smartraiden adopts the way of state machine to maintain this synchronization among nodes. Assume that we have two participants, Alice and Bob, who want to use Smartraiden as a system for off-chain transactions. For example, if Alice plans to transfer 30 tokens to Bob, by MediatedTransfer, then what will it be that synchronization states of Alice and Bob.
1.  Node synchronization when sending MediatedTransfer messages
In the event that Alice sends MediatedTransfer message to Bob, like to transfer 20 tokens, to make sure this message is the most recent one, Alice has to store data of MediatedTransfer into local storage. After Bob has received this message, the first thing to do is that, he extracts the history of records from local memory to check whether the nounce of this message is consecutive with the one before it, and to verify the balance proofs. If no faulty event, write this newest message into local storage and return a ACK to Alice. When Alice gets messages from Bob, the process of synchronization completes. If Alice goes offline before she gets any ACK from Bob, then both parties revoke all the state changes back to the one prior to Alice sending MediatedTranfer to Bob, which is atomic. After Alice goes online again, she will gets another ACK resent from Bob, then both of them starts into synchronization again. Then communication of messages  continues till all the transactions complete.

2. Node synchronization when receiving MediatedTransfer messages.
Alice sends MediatedTransfer messages to Bob, like to transfer 20 tokens, then Bob received them and verify with history record, after that Bob sends ACK message back to Alice. If Bob goes offline before sending ACK, operations of local record storage fail in order to secure atomicity of relevant operations by Bob, then all the states go back to states before Bob receiving any MediatedTransfer message. At the moment Alice has no certainty that Bob has already received messages sent by her, so that tokens in this transaction will lock up. When Bob is online, he will send another ACK message to Alice, and the state alters to one representing the lock-up state of tokens. After Alice gets ACK from Bob, both of their states start to synchronize. Then communication of messages goes on till all the transaction s complete.



