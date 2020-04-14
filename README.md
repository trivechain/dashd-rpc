# trivechaind-rpc

<a href="https://discord.gg/mZuXBxW"><img src="https://img.shields.io/discord/571241080373116928" alt="discord"></a>
<a href="https://travis-ci.org/trivechain/trivechaind-rpc"><img src="https://img.shields.io/travis/trivechain/trivechaind-rpc/master.svg" alt="travis"></a>
<a href='https://coveralls.io/github/trivechain/trivechaind-rpc?branch=master'><img src='https://coveralls.io/repos/github/trivechain/trivechaind-rpc/badge.svg?branch=master' alt='Coverage Status' /></a>
<a href="https://standardjs.com"><img src="https://img.shields.io/badge/code_style-standard-brightgreen.svg" alt="Standard - JavaScript Style Guide"></a>
<a href='https://www.npmjs.com/package/@trivechain/trivechaind-rpc'><img alt="node" src="https://img.shields.io/node/v/@trivechain/trivechaind-rpc"></a>
<a href='https://www.npmjs.com/package/@trivechain/trivechaind-rpc'><img alt="npm" src="https://img.shields.io/npm/dm/@trivechain/trivechaind-rpc"></a>
<a href="https://github.com/trivechain/trivechaind-rpc"><img alt="GitHub code size in bytes" src="https://img.shields.io/github/languages/code-size/trivechain/trivechaind-rpc"></a>

> Trivechain Client Library to connect to Trivechain Core (trivechaind) via RPC

## Install

trivechaind-rpc runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install @trivechain/trivechaind-rpc
```

## Usage

### RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 9998) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('@trivechain/trivechaind-rpc/promise')` to have promises returned
  - `require('@trivechain/trivechaind-rpc')` to have callback functions returned
	
### Examples

Config:

```javascript
var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: 19998
};
```

Promise based:

```javascript
var RpcClient = require('@trivechain/trivechaind-rpc/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })
```

Callback based (legacy):

```javascript
var run = function() {
  var bitcore = require('@trivechain/trivechaincore-lib');
  var RpcClient = require('@trivechain/trivechaind-rpc');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

### Help

You can dynamically access to the help of each method by doing

```
const RpcClient = require('@trivechain/trivechaind-rpc');
var client = new RPCclient({
    protocol:'http',
    user: 'user',
    pass: 'password', 
    host: '127.0.0.1', 
    port: 19998
});

var cb = function (err, data) {
    console.log(data)
};

// Get full help
client.help(cb);

// Get help of specific method
client.help('getinfo',cb);
```

## Contributing

Feel free to dive in! [Open an issue](https://github.com/trivechain/trivechaind-rpc/issues/new) or submit PRs.

## License

[MIT](LICENSE) &copy; Trivechain Ltd.
