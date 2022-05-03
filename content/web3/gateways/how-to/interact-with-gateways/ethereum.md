---
pcx-content-type: how-to
title: Ethereum
weight: 2
---

# Interacting with Ethereum Gateway

Once you [create a new Ethereum Gateway](/web3/gateways/how-to/#create-a-gateway), you can interact with the Ethereum network by specifying the correct JSON blob for your query.

For a list of available API methods

## Reading from the network

The Cloudflare Ethereum Gateway allows HTTP requests where the body of the
request is set to be the JSON body of the request you would like to make. For
example, if you would like to read the block that is at number `0x2244`, then
your JSON blob takes the form:

```json
{ "jsonrpc": "2.0", "method": "eth_getBlockByNumber", "params": ["0x2244", true], "id": 1 }
```

Each blob must contain a valid method parameter, the Cloudflare supported
methods are [given below](#cloudflare-supported-api). The `params` contains here contains the block number that we would like to locate, and a boolean expressing whether each individual
transaction in the block should be shown in their entirety (`true`), or as stubs
(`false`).

If we wanted to send this query to the Ethereum Gateway, then we could use a cURL command:

```bash
curl https://cloudflare-eth.com -H 'Content-Type: application/json' --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x1b4", true],"id":1}'
```

You can also write the same query using the JS fetch API:

```js
await fetch(
  new Request('https://cloudflare-eth.com', {
    method: 'POST',
    body: JSON.stringify({
      jsonrpc: '2.0',
      method: 'eth_getBlockByNumber',
      params: ['0x2244', true],
      id: 64,
    }),
    headers: {
      'Content-Type': 'application/json',
    },
  })
).then(resp => {
  return resp.json();
});
```

The response in both cases will be a JSON blob of the form:

```json
{
  "jsonrpc": "2.0",
  "id": 64,
  "result": {
    "difficulty": "0x746ef15b66",
    "extraData": "0x476574682f76312e302e302f6c696e75782f676f312e342e32",
    "gasLimit": "0x1388",
    "gasUsed": "0x0",
    "hash": "0xd6bb42034740c5d728e774e43a01f26222e0fcc279c504ca5963dc34fe70f392",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "miner": "0xf927a40c8b7f6e07c5af7fa2155b4864a4112b13",
    "mixHash": "0x975da446e302e6da6cedb3fbaa763c3c203ae88d6fab4924e2a3d34a568c4361",
    "nonce": "0x88a7f12f49151c83",
    "number": "0x2244",
    "parentHash": "0x067fd84ecdbc7491bf5ec7d5d4ead361b1f590eec74797a7f90b4a7d7004a48d",
    "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "size": "0x21b",
    "stateRoot": "0x828dade2067283e370993ec6a1bda0e65c1310e404a6d5bbb030b596eb80017c",
    "timestamp": "0x55bb040f",
    "totalDifficulty": "0x5c328da43525d",
    "transactions": [],
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "uncles": []
  }
}
```

For a full list of possible queries and examples, refer to the official [RPC
documentation](https://github.com/ethereum/execution-apis).

For a list of Cloudflare-supported methods, refer to [Supported API methods](/web3/gateways/reference/ethereum-gateway/supported-api-methods/).

## Writing to the network

Currently the Ethereum Gateway allows you to write to the network using the
`eth_sendRawTransaction` RPC method. This creates a new message call transaction
or a contract creation for signed transactions. The transactions are signed
using a secret key corresponding to your own [Ethereum
wallet](https://www.ethereum.org/use/#_3-what-is-a-wallet-and-which-one-should-i-use).
There are guides available at the previous link describing how to set up
such a wallet.

Once you have a wallet set up and a method of signing your own transactions,
you can write that transaction to the Ethereum network via the Cloudflare
Ethereum Gateway. Signed transactions use hexadecimal strings of the form:

```json
"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
```

Then you can use the Gateway to send the transaction to the network with a
cURL command:

```bash
curl https://cloudflare-eth.com -H 'Content-Type: application/json' --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"],"id":1}'
```

or using a JS fetch API request:

```js
await fetch(
  new Request('https://cloudflare-eth.com', {
    method: 'POST',
    body: JSON.stringify({
      jsonrpc: '2.0',
      method: 'eth_sendRawTransaction',
      params: [
        '0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675',
        true,
      ],
      id: 1,
    }),
    headers: {
      'Content-Type': 'application/json',
    },
  })
).then(resp => {
  return resp.json();
});
```

_(The actual command above will not work, you need to provide your own signed
transaction)_