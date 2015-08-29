---
Description: "Short post on how to create a private ethereum blockchain for testing contracts or playing around with"
Tags: [ethereum]
date: 2015-08-29T13:51:57+01:00
title: "How To Create A Private Ethereum Chain"
---

<div class="container-fluid">
	<div class="row">
		<div class="col-xs-12 col-md-3">
			<img src="/media/2015/08/ethereum-logo.png" title="Ethereum logo" class="img-responsive img-thumbnail img-circle center-block">
		</div>
		<div class="col-xs-12 col-md-9">
			<p class="lead">Setting up a private chain is useful for testing purposes or simply for playing around with, I couldn't find a good consolidated tutorial on it so I thought I would write my own.</p>
			<p>Before we get started you are going to need to have an appropriate ethereum client installed, your choices are:</p>
			<ul>
				<li>
					<a href="https://github.com/ethereum/cpp-ethereum/wiki">Eth</a> - C++ implementation
				</li>
				<li>
					<a href="https://github.com/ethereum/go-ethereum/wiki">Geth</a> - Go implementation
				</li>
			</ul>
			<p>Either will do, but for the purposes of this guide I will provide instructions on Geth. You can find good documentation on the install process over on the <a href="http://ethereum.gitbooks.io/frontier-guide/content/getting_a_client.html">frontier gitbook</a>.</p>
		</div>
	</div>
</div>

<h3>The genesis block</h3>

The crux of what we want to achieve is defined in whats called a 'genesis block', it can be likened to the first item in a linked list, or non-technically the first link in a chain. The only difference that I am aware of is that it has no reference to a previous block (or has no chain link before it). In the Bitcoin world the genesis block is [hardcoded into clients](https://en.bitcoin.it/wiki/Genesis_block) but for Ethereum it can be anything you like. You might think thats a flaw in the system being able to decide the starting conditions of the chain, but the consensus algorithm will ensure that no other node will agree with your version of the blockchain unless they have the same genesis block (and some other crucial parameters, discussed later).

Great, so how do we make one of these genesis blocks? Well its fairly simple the following JSON is all you really need:

```
{
	"nonce": "0xdeadbeefdeadbeef",
	"timestamp": "0x0",
	"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
	"extraData": "0x0",
	"gasLimit": "0x8000000",
	"difficulty": "0x400",
	"mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
	"coinbase": "0x3333333333333333333333333333333333333333",
	"alloc": {
	}
}
```

<small>Kudos to <a href="https://github.com/obscuren">obscuren</a></small>

Place this JSON into a file somewhere on your local disk and run the following command:

```
$ geth --genesis <genesis json file path> --datadir <some path to an empty folder> --networkid 123 --nodiscover --maxpeers 0 console
```

This command does a few things:

1. It utilises the genesis block json to seed the blockchain
2. It uses the datadir to store all state necessary to maintain the newly created blockchain and other data (declared to prevent you clobbering your main net data, wouldn't want to overwrite all those blocks you spent time downloading!)
3. Use a network id other than '1' to ensure we can't talk to nodes from the main network -- "connections between nodes are valid only if peers have identical protocol version and network id"
4. Disable peer discovery
5. Disable network by setting maxpeers to 0
6. Starts geth in console mode so you can interact with your new blockchain / node

From here you can follow steps in the [testing contracts and transactions](http://ethereum.gitbooks.io/frontier-guide/content/testing_contracts_and_transactions.html) git book entry on creating accounts and mining. The differences here from there are that I have elected to disable RPC, the pprof performance metric gathering processes, extra verbosity and vmdebug for simplicities sake.

Note the difficulty is set very low in the above genesis block, this allowed my local machine to mine blocks in 100's of milliseconds, that will make it very easy for you to gain ether.

<h3>Pre-seeding accounts with allocation</h3>

Once you have got the above going you may find it useful to be able to pre-seed an account with ether (to save from mining). Its fairly simple, to start with create a new blockchain and generate a new account:

```
$ geth --genesis <genesis json file path> --datadir /.../dapps/test-genesis/.ethereum --networkid 123 --nodiscover --maxpeers 0 console
  I0829 13:30:07.340636    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/blockchain
  I0829 13:30:07.342982    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/state
  I0829 13:30:07.345055    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/extra
  I0829 13:30:07.347363    3987 backend.go:291] Protocol Versions: [61 60], Network Id: 12345
  I0829 13:30:07.347738    3987 backend.go:303] Successfully wrote genesis block. New genesis hash = 82b6159155c00fb0b420046012a02257a176ad5dcfce4be4a15da39c166518e2
  I0829 13:30:07.347771    3987 backend.go:328] Blockchain DB Version: 3
  I0829 13:30:07.347866    3987 chain_manager.go:241] Last block (#0) 82b6159155c00fb0b420046012a02257a176ad5dcfce4be4a15da39c166518e2 TD=1024
  I0829 13:30:07.353373    3987 cmd.go:124] Starting Geth/v1.0.1/darwin/go1.4.2
  I0829 13:30:07.353470    3987 server.go:312] Starting Server
  I0829 13:30:07.353610    3987 backend.go:564] Server started
  I0829 13:30:07.353548    3987 server.go:549] Listening on [::]:30310
  I0829 13:30:07.353961    3987 ipc_unix.go:78] IPC service started (/.../dapps/test-genesis/.ethereum/geth.ipc)
  instance: Geth/v1.0.1/darwin/go1.4.2
   datadir: /.../dapps/test-genesis/.ethereum
   coinbase: 0x1fb891f92eb557f4d688463d0d7c560552263b5a
   at block: 0 (1970-01-01 01:00:00)
    modules: admin:1.0 db:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 shh:1.0 txpool:1.0 web3:1.0
> personal.newAccount("mypassword");
  '0x1fb891f92eb557f4d688463d0d7c560552263b5a'
```

The last command above generated a new account with address `0x1fb891f92eb557f4d688463d0d7c560552263b5a`. 

Once you've generated your account, quit geth with **&lt;ctrl-c&gt;** and remove every folder except `keystore/` from your datadir:

```
$ cd <your datadir>
$ rm -rf `ls | grep -v keystore`
```

Now for the magic, update your genesis block json, adding the following to the `alloc` key:

```
"alloc": {
	"<your account address e.g. 0x1fb891f92eb557f4d688463d0d7c560552263b5a>": {
		"balance": "10000000000000000000"
	}
}
```

Now re-run the geth command using the newly updated genesis json file and the same datadir, when check your account balance you will find you now have 10 ether:

```
$ geth --genesis <updated genesis json file path> --datadir /.../dapps/test-genesis/.ethereum --networkid 123 --nodiscover --maxpeers 0 console
  I0829 13:30:07.340636    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/blockchain
  I0829 13:30:07.342982    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/state
  I0829 13:30:07.345055    3987 database.go:74] Alloted 16MB cache to /.../dapps/test-genesis/.ethereum/extra
  I0829 13:30:07.347363    3987 backend.go:291] Protocol Versions: [61 60], Network Id: 12345
  I0829 13:30:07.347738    3987 backend.go:303] Successfully wrote genesis block. New genesis hash = 82b6159155c00fb0b420046012a02257a176ad5dcfce4be4a15da39c166518e2
  I0829 13:30:07.347771    3987 backend.go:328] Blockchain DB Version: 3
  I0829 13:30:07.347866    3987 chain_manager.go:241] Last block (#0) 82b6159155c00fb0b420046012a02257a176ad5dcfce4be4a15da39c166518e2 TD=1024
  I0829 13:30:07.353373    3987 cmd.go:124] Starting Geth/v1.0.1/darwin/go1.4.2
  I0829 13:30:07.353470    3987 server.go:312] Starting Server
  I0829 13:30:07.353610    3987 backend.go:564] Server started
  I0829 13:30:07.353548    3987 server.go:549] Listening on [::]:30310
  I0829 13:30:07.353961    3987 ipc_unix.go:78] IPC service started (/.../dapps/test-genesis/.ethereum/geth.ipc)
  instance: Geth/v1.0.1/darwin/go1.4.2
   datadir: /.../dapps/test-genesis/.ethereum
   coinbase: 0x1fb891f92eb557f4d688463d0d7c560552263b5a
   at block: 0 (1970-01-01 01:00:00)
    modules: admin:1.0 db:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 shh:1.0 txpool:1.0 web3:1.0
> primary = eth.accounts[0];
  '0x1fb891f92eb557f4d688463d0d7c560552263b5a'
> balance = web3.fromWei(eth.getBalance(primary), "ether");
  '10'
```
