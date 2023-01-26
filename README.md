<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/174559198-c1f612e5-bba2-4817-95a8-8a3c3659a2aa.png">
</p>

# Sui node setup for devnet

Official documentation:
- News Update : https://sui.io/resources-sui/testnet-wave-2-is-now-live
- Discord : https://discord.gg/sui
- Explorer : https://explorer.sui.io
- Official manual: https://docs.sui.io/build/fullnode
- Experiment with Sui DevNet: https://docs.sui.io/explore/devnet
- Check you node health: https://node.sui.zvalid.com/

## Minimum hardware requirements
- CPU: 2 CPU
- Memory: 4 GB RAM
- Disk: 50 GB SSD Storage

## Recommended hardware requirements
- CPU: 2 CPU
- Memory: 8 GB RAM
- Disk: 50 GB SSD Storage

> Storage requirements will vary based on various factors (age of the chain, transaction rate, etc) although we don't anticipate running a fullnode on devnet will require more than 50 GBs today given it is reset upon each release roughly every two weeks.

## Set up your Sui full node
### Automatic
You can setup your Sui full node in minutes by using automated script below
```
wget -O sui.sh https://raw.githubusercontent.com//catsmile100/Fullnode-SUI-Devnet/sui.sh && chmod +x sui.sh && ./sui.sh
```
## Make tests
Once the fullnode is up and running, test some of the JSON-RPC interfaces.

### Check status of your node
```
curl -s -X POST http://127.0.0.1:9000 -H 'Content-Type: application/json' -d '{ "jsonrpc":"2.0", "method":"rpc.discover","id":1}' | jq .result.info
```

### Get the five most recent transactions
```
curl --location --request POST 'http://127.0.0.1:9000/' --header 'Content-Type: application/json' \
--data-raw '{ "jsonrpc":"2.0", "id":1, "method":"sui_getRecentTransactions", "params":[5] }' | jq .
```

### Get details about a specific transaction
```
curl --location --request POST 'http://127.0.0.1:9000/' --header 'Content-Type: application/json' \
--data-raw '{ "jsonrpc":"2.0", "id":1, "method":"sui_getTransaction", "params":["<RECENT_TXN_FROM_ABOVE>"] }' | jq .
```

## Post installation
After setting up your Sui node you have to register it in the [Sui Discord](https://discord.gg/sui):
1) navigate to `#📋node-ip-application` channel
2) post your node endpoint url
```
http://<YOUR_NODE_IP>:9000/
```

## Check your node health status
Enter your node IP into https://node.sui.zvalid.com/

Healthy node should look like this:

## Generate wallet
```
echo -e "y\n\n0\n" | sui client
```
> !Please backup your wallet key files located in `$HOME/.sui/sui_config/` directory!

## Top up your wallet
1. Get your wallet address:
```
sui client active-address
```

2. Navigate to [Sui Discord](https://discord.gg/sui) `#devnet-faucet` channel and top up your wallet
```
!faucet <YOUR_WALLET_ADDRESS>
```
3. Wait until bot sends tokens to your wallet

4. You can check your balance at `https://explorer.devnet.sui.io/addresses/<YOUR_WALLET_ADDRESS>`

5. If you expand `Coins` than you can find that your wallet contains `5 unique objects` with `50000` token balances

Also you can get list of objects in your console by using command
```
sui client gas
```

## Operations with objects
Now lets do some operations with objects

### Merge two objects into one
```
JSON=$(sui client gas --json | jq -r)
FIRST_OBJECT_ID=$(echo $JSON | jq -r .[0].id.id)
SECOND_OBJECT_ID=$(echo $JSON | jq -r .[1].id.id)
sui client merge-coin --primary-coin ${FIRST_OBJECT_ID} --coin-to-merge ${SECOND_OBJECT_ID} --gas-budget 1000
```
Lets yet again check list of objects
```
sui client gas
```

We can see that two first objects are now merged into one and gas has been payed by third object

>This is only one example of transactions that can be made at the moment. Other examples can be found at the [official website](https://docs.sui.io/build/wallet)

## Usefull commands for sui fullnode
Check sui node status
```
systemctl status suid
```

Check node logs
```
journalctl -fu suid -o cat
```

Check sui client version
```
sui --version
```

Update sui version
```
wget -qO update.sh https://raw.githubusercontent.com/kj89/testnet_manuals/main/sui/tools/update.sh && chmod +x update.sh && ./update.sh
```

Update sui version with database cleanup
```
wget -qO update_db_cleanup.sh https://raw.githubusercontent.com/kj89/testnet_manuals/main/sui/tools/update_db_cleanup.sh && chmod +x update_db_cleanup.sh && ./update_db_cleanup.sh
```

## Recover your keys
Copy your keys into `$HOME/.sui/sui_config/` directory and restart the node

## Delete your node
```
systemctl stop suid
systemctl disable suid
rm -rf $HOME/.sui /usr/local/bin/sui*
```
