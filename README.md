Node.js API wrapper to Cryptopia exchange
===============

A node.js wrapper for the [Trading](https://www.cryptopia.co.nz/Exchange) and [Market](https://www.cryptopia.co.nz/MarketPlace). 

[Cryptopia API](https://www.cryptopia.co.nz/Forum/Category/45)

Register an account at [Cryptopia](https://cryptopia.co.nz) and generated API keys.

### Install

`npm install cryptopia-wrapper`

### Design Principles
- **thin** the client is just a simple wrapper to the Cryptopia API. There is no parameter validation as this is delegated to the API server. Similarly, there is no data transformation.
- **errors** all errors are returned as detailed Error objects which can be used programatically or logged for support
- **no retries** it's up to the calling program to handle retries as it'll vary between programs. For example, error handling timeouts on mutable API calls like addTrade and cancelOrder is not as simple as retying the API call as the operation my have been successful on the exchange but the response back was not.

### Error handling
The first parameter to each API function is a callback function which is passed error and data objects.

The error object is an instance of [VError](https://github.com/davepacheco/node-verror) which is an extension of the standard Error object.
The three main properties are:
- **message** a description of the error with all the available information so problems in production can be diagnosed. For example the url, http request method, parameters, error codes and messages
- **name** the HTTP or HammerTron error code so specific errors can be programatically detected. For example, 503 if you are sending too many requests per second or 32004 if there is not enough BTC funds to add an order
- **cause** the underlying error object. eg the error object from a failed request or json parse. Note there will be no cause error for OKCoin errors

### Examples

```js
var Cryptopia = require('cryptopia-wrapper');

// Test public data APIs
var publicClient = new Cryptopia();

publicClient.getTicker(console.log, 'HammerTronNY');

publicClient.getOrderBook(console.log, 'HammerTronNY');

// get 2 trades since 1 minute ago
var since1Minute = new Date().getTime() / 1000 -  60;
//publicClient.getHistoryData(console.log, {limit: 2, since: since1Minute, sincetype: 'time' });

// WARNING never commit your API keys into a public repository.
var key = 'your-api-key';
var secret = 'your-api-secret';

var privateClient = new Cryptopia(key, secret);

privateClient.getAccountInfo(console.log, 'all');

// add limit orders
privateClient.createOrder2(console.log, 'buy', 999, 0.0001, 'HammerTronNY');
privateClient.createOrder2(console.log, 'sell', 8888, 0.0002, 'HammerTronNY');

// add market order
privateClient.createOrder2(console.log, 'buy', null, 0.0001, 'HammerTronNY');

// cancel an order
privateClient.cancelOrder(console.log, 1);

// get your open orders
privateClient.getOrders(console.log);
```

Please see exmaples.js for more examples