# gamigoBTC

# (0) Useful docker commands
docker exec -i -t bob bash
docker network ls

Good to use inside docker:
Print Listening ports:
   netstat -l
Ping Bitcoin Daemon IP:
   ping btcd
Print Bitcoin Daemon Upside:
btcctl --rpccert=/rpc/rpc.cert --rpcuser=devuser --rpcpass=devpass uptime



# (1) Bitcoin Daemon

We use BTCD which is a full bitcoin node (so it downloads the complete blockchain), but 
it does not offer wallet services.

## Change Blockchain Directory
you need to change the directory in 3 locations
```
cd docker
nano docker-compose
```

## Run Bitcoin Daemon 
```
./startBtcd.sh
docker exec -i -t btcd bash
```

## Show Docker Logs
```
./logsBtcd.sh
```

## Bitcoin Controller
```
docker exec -i -t "btcd" bash
netstat -l
btcctl --rpccert=/rpc/rpc.cert --rpcuser=devuser --rpcpass=devpass version
btcctl --rpccert=/rpc/rpc.cert --rpcuser=devuser --rpcpass=devpass uptime
btcctl -u myuser -P mypass -s X.X.X.X:xxxx getpeerinfo --rpccert=rpc.cert
```

# (2) Lightening Daemon

## Run "Bob" node and bash into it:
```
export NETWORK="mainnet"
docker-compose run -d --name bob lnd_btc
docker exec -i -t bob bash
```

# Get the identity pubkey of "Bob" node:
bob$ lncli --network=simnet getinfo


lncli --network=mainnet getinfo



lnd’s authentication system is called macaroons, which are decentralized bearer credentials allowing for delegation, attenuation, and other cool features. You can learn more about them in Alex Akselrod’s writeup on Github.

Running lnd for the first time will by default generate the admin.macaroon, read_only.macaroon, and macaroons.db files that are used to authenticate into lnd. They will be stored in the network directory (default: lnddir/data/chain/bitcoin/mainnet) so that it’s possible to use a distinct password for mainnet, testnet, simnet, etc

Network Reachability
If you’d like to signal to other nodes on the network that you’ll accept incoming channels (as peers need to connect inbound to initiate a channel funding workflow), then the --externalip flag should be set to your publicly reachable IP address.


Optionally, if you’d like to have a persistent configuration between lnd launches, allowing you to simply type lnd --bitcoin.testnet --bitcoin.active at the command line, you can create an lnd.conf.

Running an lnd node means that it is listening for payments, watching the blockchain, etc. By default it is awaiting user input.

lncli is the command line client used to interact with your lnd nodes. Typically, each lnd node will be running in its own terminal window, so that you can see its log outputs. lncli commands are thus run from a different terminal window.




docker exec -i -t nodeapp /bin/bash
  499  docker exec -i -t lnd /bin/bash
