If you are brand new to the cardano space and dev, start with the overview on the developers portal - the components.

# COMPONENTS: 
Here are 3 basic components you'll definitely use;

Install cardano-node and cardano-cli( is a node js package, so you need node js on your system) because anything you do locally, will need you to run node runing whether testnet or mainnet.

cardano-wallet - API that let's you do wallet transactions through http requests.

# RESOURCES: 
More from developer portal; 1) tools - check things you can use to integrate with your technology. 2) showcase, we can find existing similar apps created. 3) testnet - get testnet ada and developing. 4)  devblog - get latest info on the developer projects. 5) docs - old docs page 6) IOHK github page. - best most current info, important to always know the latest releases to keep your software updated. the number 1 page. 7) cardano stack exchange - a stackoverflow focused on only cardano, also has a reward system. 8) others; reddit, discord, social media.

# Beginner friendly Explainer on building transactions:
 This reference is for anyone, developer or not,describing the structure of cardano transactions, how they work?

## Querying the blockchain using cardano-cli:
- Have a cardano node running in the background (can install Daedelus wallet for this), have a copy of the blockchain that it is synced up, so you can query info using cardano-cli and get updated info.
- set variables $SENDER, $RECEIVER (addresses to experiment with)
- can also check them out on cardanoscan; get basic info; balance n txs
- to get more info on these addresses n associated transactions, query the blockchain as in the examples.

### example query 1: status of blockchain

`cardano-cli query tip --mainnet` \
The results from the command show; epoch, blocks minted, current cardano era and updates loading.

- cardano-cli= powerful tool, low level, no abtraction so you see everything.

### example query 2: query utxo

`cardano-cli query utxo` \
This will show what other options you need to type to use that command ergo filters.

## Filters:

Filters help you to specify requests, example;

### example query 3: 

`cardano-cli query utxo --mainnet --address $RECEIVER` \
This command returns all the utxos associated with that address, showing the lovelaces associated with it.

This information on the cardanoscan is represented as the ADA associated with an address, but the cardano cli goes into details about how that ADA balance is spread out over the number of utxos associated with that address. 

This is important because you need to find the uxto with sufficient ADA (from that wallet address) to be consumed in a tranasaction.

Other information from the utxo query covers that native tokens in that wallet address as well.

## Sending value:

Set a variable $SENDER_KEY = "file path to a private key with ADA" \
DO NOT STORE YOUR PRIVATE KEYS ON A NETWORKED COMPUTER!
Will need to set up security for this using this [guide](./node_security)

`cardano-cli query utxo --mainnet --address $SENDER`

Result shown enables you to get input(s) - utxo(s) that you require to send the transaction, and set variables for them to reference them later in the command.

### Identifing a utxo:

We identify a utxo by the tx hash and the tx index. For example, seting a variable for TxIN1, TxIN2:
`TxIN1="00bb313a676019df9eec00305712d3f5480edbc3c972744df2b702a1b6ee4548957df0a514e1f76884e9b507ea0efac58a0404f0a4deff4523"0`
`TxIN2A="0047ec34dbc0612fe189213b1c1b3ce9acd45dc29ba8929de4e384821d372de87c0a5ee9c688c1669d51b4d79e4558e57c85053c6a6bc5d7da"1`
`TxIN2B="0047ec34dbc0612fe189213b1c1b3ce9acd45dc29ba8929de4e384821d372de87c0a5ee9c688c1669d51b4d79e4558e57c85053c6a6bc5d7db"2`

### Build a transaction:
Checklist:
1. input transaction(s)
2. sender and receiver addresses
3. path to private key

### Step one: Draft transaction
Create a draft transaction to be able to calculate fees.

transaction1:
`cardano-cli transaction build-raw --tx-in $TxIN1 --tx-out $RECEIVER+0 --tx-out $SENDER+0 --invalid-hereafter 0 --fee 0 --out-file tx1.draft`

sometimes the command in documentation is split across several lines to look like: 
<pre>cardano-cli transaction build-raw 
  --tx-in $TxIN1 
  --tx-out $RECEIVER+0 
  --tx-out $SENDER+s+0 
  --invalid-hereafter 0 
  --fee 0 
  --out-file txt1.draft 
</pre>

Running the command should atleast output a protocol file containing protocol parameters \
use `ls` command to check files in the directory.

transaction2:
`cardano-cli transaction build-raw --tx-in $TxIN2A --tx-in $TxIN2B --tx-out $RECEIVER+0 --tx-out $SENDER+0 --invalid-hereafter 0 --fee 0 --out-file tx2.draft`

Visualising txt1.draft in a text editor: using `nano txt1.draft` command should show a json object containing type, description and cborHex values.

To view more information encoded in the cborHex value: \
`cardano-cli transaction view --tx-body-file tx1.draft`

### Step two: Calculate fee
Specify the count of the inputs, outputs, witnesses/signatures. \
`cardano-cli transaction calculate-min-fee --tx-body-file tx1.draft --tx-in-count 1 --tx-out-count 2 --address-count 1 --mainnet --protocol-params-file protocol.json`

Note:
- For multisig configuration, you'll need to set more signature count.
- Signing a transaction can happen offline?! 
- For plutus scripts we can tell that besides wallet addresses, we can have contract addresses. Contract addresses are automated scripts that can for example look at an oracle and determine if something is true, if it is, then sign the transaction.

The command should output the number of lovelance required for this particular transaction.

From often use of Daedelus wallet, common fee noted for a transaction with one utxo in, two utxos out, is 0.176413 ADA. \
Fees vary according to changes in:
- number of inputs,
- number of outputs,
- including metadata,
- minting of native assests

`cardano-cli transaction calculate-min-fee --tx-body-file tx2.draft --tx-in-count 2 --tx-out-count 2 --address-count 1 --mainnet --protocol-params-file protocol.json`

Save the fees for both transactions as variables \
`FEE1=176413`
`FEE2=179757`

Calculating fees depends on the make up of the wallet and so building wallets can be hard because of fee calculation because you do not know the distribution of utxos in a particular wallet. \
Combining many small utxos for one transaction then becomes more expensive than using one big utxo.

To build an acceptable transaction, one has to make sure one is conserving every single lovelace, so the arithmetic has to be exactly correct for the transaction to work.

### Step three: Calculate the change
`CHANGE1=823587`
`CHANGE2=2017059`

It's a good thing wallets are able to calculate all this for us.

### Step three: Set expiration time

Query the tip of the mainnet to get the slot number. \
`cardano-cli query tip --mainnet`

One slot = 1 sec, protocol parameter for now

We need to get a slot number to be able to set an expiration time, for this example, we can just add 1000 slots. \ 
So taking into account finishing the building, submiting of these transactions within the next 15mins(<1000 slots) we should set the expire variable to the current slot plus 1000.

`EXPIRE="$calculated_expiration_time1"`

This is important because: \
We shouldn't keep potential transactions floating around way after thier expiration times.

The concurrency issue - building and signing a transaction, and then having the parameters of the transaction disappear e.g used up by another transaction before it is submitted equals to failure. 

Having different uxtos from the same place being used by different people to submit transactions at the same time.

### Step five: build the transaction(s)

Set final variables; $SEND for ammount to be sent.

`cardano-cli transaction build-raw --tx-in $TxIN1 --tx-out $RECEIVER+$SEND --tx-out $SENDER+$CHANGE1 --invalid-heresafter $EXPIRE --fee $FEE1 -out-file tx1.raw`

Running the command should atleast output the raw transaction file.

`cardano-cli transaction build-raw --tx-in $TxIN2A --tx-in $TxIN2B --tx-out $RECEIVER+$SEND --tx-out $SENDER+CHANGE2 --invalid-heresafter $EXPIRE --fee $FEE2 -out-file tx2.raw` 


## References:
- https://www.youtube.com/watch?v=XVHwWEbExOo
- https://www.youtube.com/watch?v=AZXcrBx9dw4
- https://developers.cardano.org/
