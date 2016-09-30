# Contract API 

Compared to other blockchains, contracts lie at the heart of Ethereum's unique value proposition. Contracts can be expressively programmed in languages like [Solidity](https://solidity.readthedocs.io/en/latest/); if you're not familiar with Ethereum's contract language you should definitely start there.

We offer a number of API endpoints that significantly simplify contract creation and method calling. Via the methods below, you can embed new contracts into the Ethereum blockchain, check their code and ABI, and initiate contract methods and execution. Essentially, we provide a _JSON/HTTP binding for your Ethereum contracts_.

With great power comes great responsibility; in other words, it's easier to shoot yourself in the foot with Ethereum. Don't <a href="https://en.wikipedia.org/wiki/Decentralized_autonomous_organization#Security">The DAO it.</a> Follow best <a href="https://github.com/ethereum/wiki/wiki/Safety">security and safety practices</a> when coding your smart contracts.

<aside class="warning">
Several of the requests below need a private key to pay for contract gas. We feel this is an acceptable security tradeoff to make the API much easier to use, given that for development you don't need a lot of money to push and call contracts. Just keep a minimal amount of ether on your development key, and if you need to fund your contract with more ether, use a different key (and keep it safe).
</aside>

## Create Contract Endpoint

```shell
# Check solidity compilation via non-published test
# Using "greeter" contract solidity example, the "hello world" of Ethereum
cat testGreeter.json
{
  "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
  "params": ["Hello BlockCypher Test"]
}
curl -sd @testGreeter.json https://api.blockcypher.com/v1/eth/main/contracts?token=YOURTOKEN
[
  {
    "name": "greeter",
    "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
    "bin": "606060405260405161023e38038061023e8339810160405280510160008054600160a060020a031916331790558060016000509080519060200190828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f10609f57805160ff19168380011785555b50608e9291505b8082111560cc57600081558301607d565b50505061016e806100d06000396000f35b828001600101855582156076579182015b82811115607657825182600050559160200191906001019060b0565b509056606060405260e060020a600035046341c0e1b58114610026578063cfae321714610068575b005b6100246000543373ffffffffffffffffffffffffffffffffffffffff908116911614156101375760005473ffffffffffffffffffffffffffffffffffffffff16ff5b6100c9600060609081526001805460a06020601f6002600019610100868816150201909416939093049283018190040281016040526080828152929190828280156101645780601f1061013957610100808354040283529160200191610164565b60405180806020018281038252838181518152602001915080519060200190808383829060006004602084601f0104600f02600301f150905090810190601f1680156101295780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b565b820191906000526020600020905b81548152906001019060200180831161014757829003601f168201915b505050505090509056",
    "abi": [
      {
        "constant": false,
        "inputs": [],
        "name": "kill",
        "outputs": [],
        "type": "function"
      },
      {
        "constant": true,
        "inputs": [],
        "name": "greet",
        "outputs": [
          {
            "name": "",
            "type": "string"
          }
        ],
        "type": "function"
      },
      {
        "inputs": [
          {
            "name": "_greeting",
            "type": "string"
          }
        ],
        "type": "constructor"
      }
    ],
    "params": [
      "Hello BlockCypher Test"
    ]
  },
  {
    "name": "mortal",
    "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
    "bin": "606060405260008054600160a060020a03191633179055605c8060226000396000f3606060405260e060020a600035046341c0e1b58114601a575b005b60186000543373ffffffffffffffffffffffffffffffffffffffff90811691161415605a5760005473ffffffffffffffffffffffffffffffffffffffff16ff5b56",
    "abi": [
      {
        "constant": false,
        "inputs": [],
        "name": "kill",
        "outputs": [],
        "type": "function"
      },
      {
        "inputs": [],
        "type": "constructor"
      }
    ],
    "params": [
      "Hello BlockCypher Test"
    ]
  }
]

# Now publish the greeter contract 
cat publishGreeter.json
{
  "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
  "params": ["Hello BlockCypher Test"],
  "publish": ["greeter"],
  "private": "3ca40...",
  "gas_limit": 500000
}
curl -d @publishGreeter.json https://api.blockcypher.com/v1/eth/main/contracts?token=YOURTOKEN
[
  {
    "name": "greeter",
    "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
    "bin": "606060405260405161023e38038061023e8339810160405280510160008054600160a060020a031916331790558060016000509080519060200190828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f10609f57805160ff19168380011785555b50608e9291505b8082111560cc57600081558301607d565b50505061016e806100d06000396000f35b828001600101855582156076579182015b82811115607657825182600050559160200191906001019060b0565b509056606060405260e060020a600035046341c0e1b58114610026578063cfae321714610068575b005b6100246000543373ffffffffffffffffffffffffffffffffffffffff908116911614156101375760005473ffffffffffffffffffffffffffffffffffffffff16ff5b6100c9600060609081526001805460a06020601f6002600019610100868816150201909416939093049283018190040281016040526080828152929190828280156101645780601f1061013957610100808354040283529160200191610164565b60405180806020018281038252838181518152602001915080519060200190808383829060006004602084601f0104600f02600301f150905090810190601f1680156101295780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b565b820191906000526020600020905b81548152906001019060200180831161014757829003601f168201915b505050505090509056",
    "abi": [
      {
        "constant": false,
        "inputs": [],
        "name": "kill",
        "outputs": [],
        "type": "function"
      },
      {
        "constant": true,
        "inputs": [],
        "name": "greet",
        "outputs": [
          {
            "name": "",
            "type": "string"
          }
        ],
        "type": "function"
      },
      {
        "inputs": [
          {
            "name": "_greeting",
            "type": "string"
          }
        ],
        "type": "constructor"
      }
    ],
    "gas_limit": 500000,
    "creation_tx_hash": "61474003e56d67aba6bf148c5ec361e3a3c1ceea37fe3ace7d87759b399292f9",
    "address": "0eb688e79698d645df015cf2e9db5a6fe16357f1",
    "params": [
      "Hello BlockCypher Test"
    ]
  },
  {
    "name": "mortal",
    "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
    "bin": "606060405260008054600160a060020a03191633179055605c8060226000396000f3606060405260e060020a600035046341c0e1b58114601a575b005b60186000543373ffffffffffffffffffffffffffffffffffffffff90811691161415605a5760005473ffffffffffffffffffffffffffffffffffffffff16ff5b56",
    "abi": [
      {
        "constant": false,
        "inputs": [],
        "name": "kill",
        "outputs": [],
        "type": "function"
      },
      {
        "inputs": [],
        "type": "constructor"
      }
    ],
    "gas_limit": 500000,
    "params": [
      "Hello BlockCypher Test"
    ]
  }
]

# you can check the creation_tx_hash to see that the contract was published
curl -s https://api.blockcypher.com/v1/eth/main/txs/61474003e56d67aba6bf148c5ec361e3a3c1ceea37fe3ace7d87759b399292f9
{
  "block_hash": "fcd88715c95e6082612ab673ae3053d8bbfc51ca0b00aa82974ea4d139010015",
  "block_height": 1917071,
  "block_index": 0,
  "hash": "61474003e56d67aba6bf148c5ec361e3a3c1ceea37fe3ace7d87759b399292f9",
  "addresses": [
    "e2d7871ca37379ea18d259cdf3592fe032183d94"
  ],
  "total": 0,
  "fees": 8448706000000000,
  "size": 756,
  "gas_used": 206066,
  "gas_price": 41000000000,
  "contract_creation": true,
  "relayed_by": "",
  "confirmed": "2016-07-20T01:54:50Z",
  "received": "2016-07-20T01:54:50Z",
  "ver": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 5,
  "confidence": 1,
  "inputs": [
    {
      "sequence": 7,
      "addresses": [
        "e2d7871ca37379ea18d259cdf3592fe032183d94"
      ]
    }
  ],
  "outputs": [
    {
      "value": 0,
      "script": "606060405260405161023e38038061023e8339810160405280510160008054600160a060020a031916331790558060016000509080519060200190828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f10609f57805160ff19168380011785555b50608e9291505b8082111560cc57600081558301607d565b50505061016e806100d06000396000f35b828001600101855582156076579182015b82811115607657825182600050559160200191906001019060b0565b509056606060405260e060020a600035046341c0e1b58114610026578063cfae321714610068575b005b6100246000543373ffffffffffffffffffffffffffffffffffffffff908116911614156101375760005473ffffffffffffffffffffffffffffffffffffffff16ff5b6100c9600060609081526001805460a06020601f6002600019610100868816150201909416939093049283018190040281016040526080828152929190828280156101645780601f1061013957610100808354040283529160200191610164565b60405180806020018281038252838181518152602001915080519060200190808383829060006004602084601f0104600f02600301f150905090810190601f1680156101295780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b565b820191906000526020600020905b81548152906001019060200180831161014757829003601f168201915b5050505050905090560000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000001648656c6c6f20426c6f636b437970686572205465737400000000000000000000",
      "addresses": [
        "0eb688e79698d645df015cf2e9db5a6fe16357f1"
      ]
    }
  ]
}
```

The Create Contract Endpoint allows you to submit your **solidity** code and **params** to check raw serialized binary compilation and [ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI). It's an easy to validate your contract compiles before pushing it to the Ethereum blockchain.

If you include a **private** key (associated with a funded Ethereum external account), **gas_limit**, and contract(s) to **publish**, BlockCypher will embed the contract into the blockchain and return the transaction hash that created the contract and the contract address. Find both of those properties under the returned contract object as **address** and **creation_tx_hash**.

The **params** property lets you provide arguments to the contract constructor. If your contract has no constructor or the constructor takes no arguments, this property can be omitted.

You can optionally include **value** in wei to transfer to the contract on creation.

Note that for now both the contract solidity source and the ABI are made publicly available for anyone with the contract hash. We will support making the source private to your token in the future. However note that all contracts on the public Ethereum blockchain can be seen in binary form and it's only a matter of time before good decompilers surface.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/contracts | POST | [Contract](#contract) | Array[[Contract](#contract)]

## Get Contract Address Endpoint

```shell

# and/or, check the contract address. if it was created by blockcypher, we'll return the code and ABI as well.
curl -s https://api.blockcypher.com/v1/eth/main/contracts/0eb688e79698d645df015cf2e9db5a6fe16357f1?token=YOURTOKEN
{
  "solidity": "contract mortal {\n  /* Define variable owner of the type address*/\n    address owner;\n    /* this function is executed at initialization and sets the owner of the contract */\n    function mortal() { owner = msg.sender; }\n    /* Function to recover the funds on the contract */\n    function kill() { if (msg.sender == owner) suicide(owner); }\n}\n\ncontract greeter is mortal {\n    /* define variable greeting of the type string */\n    string greeting;\n    /* this runs when the contract is executed */\n    function greeter(string _greeting) public {\n        greeting = _greeting;\n    }\n    /* main function */\n    function greet() constant returns (string) {\n        return greeting;\n    }\n}",
  "bin": "606060405260405161023e38038061023e8339810160405280510160008054600160a060020a031916331790558060016000509080519060200190828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f10609f57805160ff19168380011785555b50608e9291505b8082111560cc57600081558301607d565b50505061016e806100d06000396000f35b828001600101855582156076579182015b82811115607657825182600050559160200191906001019060b0565b509056606060405260e060020a600035046341c0e1b58114610026578063cfae321714610068575b005b6100246000543373ffffffffffffffffffffffffffffffffffffffff908116911614156101375760005473ffffffffffffffffffffffffffffffffffffffff16ff5b6100c9600060609081526001805460a06020601f6002600019610100868816150201909416939093049283018190040281016040526080828152929190828280156101645780601f1061013957610100808354040283529160200191610164565b60405180806020018281038252838181518152602001915080519060200190808383829060006004602084601f0104600f02600301f150905090810190601f1680156101295780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b565b820191906000526020600020905b81548152906001019060200180831161014757829003601f168201915b5050505050905090560000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000001648656c6c6f20426c6f636b437970686572205465737400000000000000000000",
  "abi": "[{\"constant\":false,\"inputs\":[],\"name\":\"kill\",\"outputs\":[],\"type\":\"function\"},{\"constant\":true,\"inputs\":[],\"name\":\"greet\",\"outputs\":[{\"name\":\"\",\"type\":\"string\"}],\"type\":\"function\"},{\"inputs\":[{\"name\":\"_greeting\",\"type\":\"string\"}],\"type\":\"constructor\"}]",
  "creation_tx_hash": "61474003e56d67aba6bf148c5ec361e3a3c1ceea37fe3ace7d87759b399292f9",
  "created": "2016-07-20T01:54:50Z",
  "address": "0eb688e79698d645df015cf2e9db5a6fe16357f1"
}
```

Resource | Method | Return Object
-------- | ------ | -------------
/contract/$ADDRESS | GET | [Contract](#contract)

ADDRESS is a *string* representing the contract address you're interested in querying, for example: 

`0eb688e79698d645df015cf2e9db5a6fe16357f1`

The returned object contains information about the contract; if you deployed the contract with BlockCypher, it will return **solidity** and **abi** as well.

## Call Contract Method Endpoint

```shell
# call the greet method, using your own private key and gas amount to fund
cat call.json
{
	"private": "3ca40...",
	"gas_limit": 20000 
}
curl -d @call.json -s https://api.blockcypher.com/v1/eth/main/contracts/0eb688e79698d645df015cf2e9db5a6fe16357f1/greet?token=YOURTOKEN
{
  "gas_limit": 20000,
  "address": "0eb688e79698d645df015cf2e9db5a6fe16357f1",
  "results": [
    "Hello BlockCypher Test"
  ]
}

# use the same private key/gas amount to call the "kill" method, which causes the contract to create an internal_tx to refund the initial private key
curl -d @call.json -s https://api.blockcypher.com/v1/eth/main/contracts/0eb688e79698d645df015cf2e9db5a6fe16357f1/kill?token=YOURTOKEN
{"error": "Error calling contract: Gas value too low, intrinsic gas required is 53272.."}

# whoops! let's try upping the gas
cat call.json
{
	"private": "3ca40...",
	"gas_limit": 100000 
}
curl -d @call.json -s https://api.blockcypher.com/v1/eth/main/contracts/0eb688e79698d645df015cf2e9db5a6fe16357f1/kill?token=YOURTOKEN
{
  "gas_limit": 100000,
  "call_tx_hash": "42dc5862aeedf1001db37c404c8dc922918167589aba1e0b19c6a9372eedb294",
  "address": "0eb688e79698d645df015cf2e9db5a6fe16357f1"
}
```

The Call Contract Method endpoint makes every method in your contracts callable simply via an HTTP request. It's a binding that translates a published contract into a set of endpoints (one for each method) and a provided JSON array into a set of arguments to invoke a given method.

To call a method on a given contract, you must include a **private** key associated with a funded external account and a specified **gas_limit** in your request object. **params** are optionally accepted if the contract method allows them. Make sure the JSON types your provide match your contract signature (string, number, etc.). You can optionally include **value** in wei to transfer to this contract method.

The Call Contract endpoint will check the contract ABI to determine whether the method has been declared "constant". If so, no transaction will be created and no gas will be consumed. The method is just called locally on our servers and won't be registered on the blockchain. Otherwise, we will build the call transaction to invoke the method on the Ethereum blockchain and propagate it on the network. Keep in mind that in that case, you will need to wait for the call transaction to be included in a block to see its effects.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/contracts/$ADDRESS/$METHOD | POST | [Contract](#contract) | [Contract](#contract)

ADDRESS is a *string* representing the contract address you're interested in querying, for example: 

`0eb688e79698d645df015cf2e9db5a6fe16357f1`

METHOD is a *string* representing a declared method from the above contract; in the above example, the options are:

`greet`

or

`kill`
