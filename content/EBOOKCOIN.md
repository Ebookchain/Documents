FORMAT: 1A

# Ebookcoin API

Ebookcoin Wallet REST API. All api using **/api** prefix.

**How to work with API**

+ Information need to return.
+ Success parameter. Determines the success of a response.
+ Error perameter. Provided if the success parameter equal **"false"**.

API available only after wallet loading, before all routes will return:

    {
        "success" : false,
        "error" : "loading"
    }


# Group Accounts

Accounts manage api.

## Open account [/api/accounts/open]

Open account in wallet.

**Previous version:** /api/unlock

### Return account [POST]

+ Request (application/json)

```
{
    "secret" : "secret key of account"
}
```

+ Response 200 (application/json)

```        
{
    "success": true,
    "account": {
        "address": "Address of account. string",
        "unconfirmedBalance": "Unconfirmed balance of account. integer",
        "balance": "Balance of account. integer",
        "publicKey": "Public key of account. Hex",
        "unconfirmedSignature": "If account enabled second signature, but it's still not confirmed. Boolean: true or false",
        "secondSignature": "If account enabled second signature. Boolean: true or false",
        "secondPublicKey": "Second signature public key. Hex",
        "username" : "Username of account."
    }
}
```

## Get balance [/api/accounts/getBalance?address={address}]

Get balance of account.

**Previous version:** /api/getBalance

### Retrive balance of account [GET]

+ Parameters
    + address (string) ... Address of account

+ Response 200 (application/json)

        {
            "success" : true,
            "balance" : "Balance of account",
            "unconfirmedBalance" : "Unconfirmed balance of account"
        }

## Get account public key [/api/accounts/getPublicKey?address={address}]

Get public key of account. If the account does not exist (not opened) will return error.

**Previous version:** nothing.

### Retrive public key of account [GET]

+ Parameters
    + address (string) ... Address of account

+ Response 200 (application/json)

        {
            "success": true,
            "publicKey": "Public key of account. Hex"
        }

## Generate public key [/api/accounts/generatePublicKey]

Will return public key of provided secret key.

**Previous version:** nothing.

### Retrive public key [POST]

+ Request (application/json)

        {
            "secret" : "secret key of account"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "publicKey": "Public key of account. Hex"
        }


## Get account [/api/accounts?address={address}]

Will return account by address.

**Previous version:** nothing.

### Retrive account [GET]

+ Parameters
    + address (string) ... Address of account

+ Response 200 (application/json)

        {
            "success": true,
            "account": {
                "address": "Address of account. String",
                "unconfirmedBalance": "Unconfirmed balance of account. Integer",
                "balance": "Balance of account. Integer",
                "publicKey": "Public key of account. Hex",
                "unconfirmedSignature": "If account enabled second signature, but it's still not confirmed. Boolean: true or false",
                "secondSignature": "If account enabled second signature. Boolean: true or false",
                "secondPublicKey": "Second signature public key. Hex"
            }
        }

## Get delegates [/api/accounts/delegates?address={address}]

Will return account's delegates by address.

**Previous version:** nothing.

### Retrive delegates [GET]

+ Parameters
    + address (string) ... Address of account

+ Response 200 (application/json)

        {
            "success": true,
            "delegates": [array]
        }

## Put delegates [/api/accounts/delegates]

Will vote for selected delegates.

**Previous version:** nothing.

### Vote for delegates [PUT]

+ Request (application/json)

        {
            "secret" : "Secret key of account",
            "publicKey" : "Public key of sender account, to verify secret passphrase in wallet. Optional, only for UI",
            "secondSecret" : "Secret key from second transaction, required if user uses second signature",
            "delegates" : "Array of string in the following format: ["+DelegatePublicKey"] OR ["-DelegatePublicKey"]. Use + to UPvote, - to DOWNvote"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transaction": {object}
        }

# Group Loader

Loader API.

Providing sync & loading information.
Only this API working when wallet on loading.

## Get loading status [/api/loader/status]

Get status of wallet at loading time.

### Retrive loading status [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "loaded": "Is blockchain loaded? Boolean: true or false",
            "now": "Last block loaded during loading time. Integer",
            "blocksCount": "Total blocks count in blockchain at loading time. Integer"
        }

## Get synchronization status [/api/loader/status/sync]

Get synchronization status of wallet.

### Retrive synchronization status [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "sync": "Is wallet is syncing with another peers? Boolean: true or false",
            "blocks": "Number of blocks remaining to sync. Integer",
            "height": "Total blocks in blockchain. Integer"
        }



# Group Transactions

Transactions API.

## Get list of transactions [/api/transactions?blockId={blockId}&senderId={senderId}&recipientId={recipientId}&limit={limit}&offset={offset}&orderBy={field}]

**Previous version:** /api/getAddressTransactions.

Transactions list matched by provided parameters.

### Retrive list of transactions [GET]

All parameters joins by "OR".

**Example:**

For request:
/api/transactions?blockId=10910396031294105665&senderId=6881298120989278452C&orderBy=timestamp:desc

It's looks like:
blockId=10910396031294105665 **OR** senderId=6881298120989278452C

+ Parameters
    + blockId (optional, string) ... Block id of transaction.
    + senderId (optional, string) ... Sender address of transaction.
    + recipientId (optional, string) ... Recipient of transaction.
    + limit (optional, number) ... Limit of transactions to send in response. Default is 20.
    + offset (optional, number) ... Offset to load. Integer.
    + orderBy (optional, string) ... Name of column to order. After column name must go "desc" or "acs" to choose order type, prefix for column name is t_. Example: orderBy=t_timestamp:desc


+ Response 200 (application/json)

        {
            "success" : true,
            "transactions" : ["list of transactions objects"]
        }

## Send transaction [/api/transactions]

Send transaction to broadcast network.

### Sending transaction [PUT]

**Previous version:** /api/sendFunds

+ Request (application/json)

        {
            "secret" : "Secret key of account",
            "amount" : /* Amount of transaction * 10^8. Example: to send 1.1234 XCR, use 112340000 as amount */,
            "recipientId" : "Recipient of transaction. Address or username.",
            "publicKey" : "Public key of sender account, to verify secret passphrase in wallet. Optional, only for UI",
            "secondSecret" : "Secret key from second transaction, required if user uses second signature"
        }

+ Response 200 (application/json)

        {
            "success" : true,
            "transactionId": "id of added transaction"
        }

## Get transaction [/api/transactions/get?id={id}]

Transaction matched by id.

**Previous version:** nothing.

### Retrive transaction by id [GET]

+ Parameters
    + id (string) ... String of transaction.

+ Response 200 (application/json)

        {
            "success": true,
            "transaction": {
                "id": "Id of transaction. String",
                "type": "Type of transaction. Integer",
                "subtype": "Subtype of transaction. Integer",
                "timestamp": "Timestamp of transaction. Integer",
                "senderPublicKey": "Sender public key of transaction. Hex",
                "senderId": "Address of transaction sender. String",
                "recipientId": "Recipient id of transaction. String",
                "amount": "Amount. Integer",
                "fee": "Fee. Integer",
                "signature": "Signature. Hex",
                "signSignature": "Second signature. Hex",
                "companyGeneratorPublicKey": "If transaction was sent to merchant, provided comapny generator public key to find company. Hex",
                "confirmations": "Number of confirmations. Integer"
            }
        }

## Get unconfirmed transaction [/api/transactions/unconfirmed/get?id={id}]

Get unconfirmed transaction by id.

**Previous version:** nothing.

### Retrive unconfirmed transaction by id [GET]

+ Parameters
    + id (string) ... String of unconfirmed transaction.

+ Response 200 (application/json)

        {
            "success" : true,
            "transaction": {
                "id": "Id of transaction. String",
                "type": "Type of transaction. Integer",
                "subtype": "Subtype of transaction. Integer",
                "timestamp": "Timestamp of transaction. Integer",
                "senderPublicKey": "Sender public key of transaction. Hex",
                "senderId": "Address of transaction sender. String",
                "recipientId": "Recipient id of transaction. String",
                "amount": "Amount. Integer",
                "fee": "Fee. Integer",
                "signature": "Signature. Hex",
                "signSignature": "Second signature. Hex",
                "confirmations": "Number of confirmations. Integer"
            }
        }

## Get list of unconfirmed transactions [/api/transactions/unconfirmed]

Get list of unconfirmed transactions

**Previous version:** nothing.

### Retrive list of unconfirmed transactions [GET]

+ Response 200 (application/json)

        {
            "success" : true,
            "transactions" : [list of transaction objects]
        }

# Group Peers

## Get peers list [/api/peers?state={state}&os={os}&shared={shared}&version={version}&limit={limit}&offset={offset}&orderBy={orderBy}]

Get peers list by parameters.

All parameters joins by **OR**.

**Example:**
/api/peers?state=1&version=0.1.8

Will looks like: state=1 OR version=0.1.8

### Retrive peers list [GET]

+ Parameters
    + state (string, optional) ... State of peer. 1 - disconnected. 2 - connected. 0 - banned.
    + os (string, optional) ... OS of peer.
    + shared (string, optional) ... is peer shared? Boolean: true or false.
    + version (string, optional) ... Version of peer.
    + limit (integer, optional) ... Limit to show. Max limit is 100.
    + offset (integer, optional) ... Offset to load. Integer.
    + orderBy (optional, string) ... Name of column to order. After column name must go "desc" or "acs" to choose order type.

+ Response 200 (application/json)

        {
            "success" : true,
            "peers" : ["list of peers"]
        }

## Get peer [/api/peers/get?ip={ip}&port={port}]

Get peer by ip and port

### Retrive peer [GET]

+ Parameters
    + ip (string) ... Ip of peer.
    + port (integer) ... Port of peer.


+ Response 200 (application/json)

        {
            "success" : true,
            "peer" : "peer object"
        }

## Get peer version, build time [/api/peers/version]

Get peer version and build time

### Retrive version info [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "version" : "version of Ebookcoin",
            "build" : "time of build"
        }

# Group Blocks

Blocks manage API.

## Get block [/api/blocks/get?id={id}]

### Get block by id [GET]

Get block by id.

**Previous version:** /api/getBlock

+ Parameters
    + id (string) .. Id of block.

+ Response 200 (application/json)

        {
            "success": true,
            "block": {
                "id": "Id of block. String",
                "version": "Version of block. Integer",
                "timestamp": "Timestamp of block. Integer",
                "height": "Height of block. Integer",
                "previousBlock": "Previous block id. String",
                "numberOfRequests": "Not using now. Will be removed in 0.2.0",
                "numberOfTransactions": "Number of transactions. Integer",
                "numberOfConfirmations": "Not using now.",
                "totalAmount": "Total amount of block. Integer",
                "totalFee": "Total fee of block. Integer",
                "payloadLength": "Payload length of block. Integer",
                "requestsLength": "Not using now. Will be removed in 0.2.0",
                "confirmationsLength": "Not using now.,
                "payloadHash": "Payload hash. Hex",
                "generatorPublicKey": "Generator public key. Hex",
                "generatorId": "Generator id. String.",
                "generationSignature": "Generation signature. Not using. Will be removed in 0.2.0",
                "blockSignature": "Block signature. Hex"
            }
        }

## Get blocks [/api/blocks?generatorPublicKey={generatorPublicKey}&height={height}&previousBlock={previousBlock}&totalAmount={totalAmount}&totalFee={totalFee}&limit={limit}&offset={offset}&orderBy={orderBy}]

All parameters joins by **OR**.

**Example:**
/api/blocks?height=100&totalAmount=10000

Will looks like: height=100 OR totalAmount=10000

**Previous version:** /api/getLastBlocks

### Retrive blocks list [GET]

+ Parameters
    + totalFee (optional, integer) ... total fee of block.
    + totalAmount (optional, integer) ... total amount of block.
    + previousBlock (optional, string) ... previous block of need block.
    + height (optional, integer) ... height of block.
    + generatorPublicKey (optional, string) ... generator id of block in hex.
    + limit (optional, number) ... limit of blocks to add to response. Default to 20.
    + offset (optional, number) ... offset to load blocks. Integer.
    + orderBy (optional, string) ... field name to order by. Format: fieldname:orderType. Example: height:desc, timestamp:asc

+ Response 200 (application/json)

        {
            "success" : true,
            "blocks" : ["array of blocks"]
        }

## Get blockchain fee percent [/api/blocks/getFee]

**Previous version:** /api/getFee

### Retrive blockchain fee percent [GET]

+ Response 200 (application/json)

        {
            "success" : true,
            "fee" : "fee percent"
        }

## Get blockchain height [/api/blocks/getHeight]

Get blockchain height.

**Previous version:** /api/getHeight

### Retrive height of blockchain [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "height": "Height of blockchain. Integer"
        }

## Get forged by account [/api/delegates/forging/getForgedByAccount?generatorPublicKey={generatorPublicKey}]

Get amount forged by account.

### Retrive forged by account [GET]

+ Parameters
    + generatorPublicKey (string) ... Account public key. Hex

+ Response 200 (application/json)

        {
            "success" : true,
            "sum" : "Forged amount. Integer"
        }

# Group Signatures

## Get signature [/api/signatures/get?id={id}]

Get second signature of account.

**Previous version:** nothing.

### Retrive signature [GET]

+ Parameters
    + id (optional, string) ... Id of signature

+ Response 200 (application/json)

        {
            "success" : true,
            "signature" : {
                "id" : "Id. String",
                "timestamp" : "TimeStamp. Integer",
                "publicKey" : "Public key of signature. hex",
                "generatorPublicKey" : "Public Key of Generator. hex",
                "signature" : [array],
                "generationSignature" : "Generation Signature"
            }
        }

## Add second signature [/api/signatures]

Add second signature to account.

**Previous version:** /api/addPassPhrase

### Put second signature for account [PUT]

+ Request (application/json)

        {
            "secret" : "secret key of account",
            "secondsecret" : "second key of account",
            "publicKey" : "optional, to verify valid secret key and account"
        }

+ Response 200 (application/json)

        {
            "success" : true,
            "transactionId" : "id of transaction with new signature",
            "publicKey" : "Public key of signature. hex"
        }

# Group Delegates

## Enable delegate on account [/api/delegates]

Calls for delegates functional.

### Enable delegate on account [PUT]

Get delegate of account.

+ Request (application/json)

        {
            "secret" : "Secret key of account",
            "secondSecret": "Second secret of account",
            "username" : "Username of delegate. String from 1 to 20 characters."
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transaction": "transaction object"
        }


## Get delegates [/api/delegates?limit={limit}&offset={offset}&orderBy={orderBy}]

Get delegates list.

### Get delegates [GET]

+ Parameters

    + limit (optional, integer) ... Limit to show. Integer. Maximum is 100.
    + offset (optional, integer) ... Offset. Integer.
    + orderBy (optional, string) ... Order by field.

+ Response 200 (application/json)

        {
            "success": true,
            "delegates": "delegates objects array"
        }

## Get delegate [/api/delegates/get?id={transactionId}]

Get delegate by transaction id.

### Get delegate [GET]

+ Parameters

    + transactionId (optional,string) ... Id of transaction where delegated was putted.

+ Response 200 (application/json)

        {
            "success": true,
            "delegate": {
                "username": "username of delegate",
                "transactionId": "transaction id",
                "votes": "amount of stake voted for this delegate"
        }

## Get votes of account [/api/accounts/delegates/?address={address}]

Get votes by account address.

### Get votes [GET]

+ Parameters

    + address (required, string) ... Address of account.

+ Response 200 (application/json)

        {
            "success": true,
            "delegates": "array of delegates"
        }

## Enable forging on delegate [/api/delegates/forging/enable]

Enable forging

### Enable forging [POST]

+ Request (application/json)

        {
            "secret" : "secret key of delegate account"
        }

+ Response 200 (application/json)

        {
            "success" : true,
            "address": "address"
        }

## Disable foring on delegate [/api/delegates/forging/disable]

Disable forging

## Disable forging [POST]

+ Request (application/json)

        {
            "secret": "secret key of delegate account"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "address": "address"
        }

## Get status of forging [/api/forging/status]

Get status of forging.

### Get status of forging

+ Request (application/json)

        {
            "publicKey" : "public key of account"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "enabled": "Forging is enabled on this account or not?"
        }

## Get voters [/api/delegates/voters?publicKey={publicKey}]

Get voters of delegate

### Get voters [GET]

+ Parameters
    + publicKey (required, string) ... public key of delegate.

+ Response 200 (application/json)

        {
            "success": true,
            "accounts": ["array of accounts who vote for delegate"]
        }

# Group Messages

## Send message [/api/messages]

Send message to recipient

### Send message [PUT]

+ Request (applicatoion/json)

        {
            "recipientId" : "id of recipient. String. Optional.",
            "encrypt" : "encrypt message or not encrypt. Boolean. Default to false. If equal true need to provide recipient public key.",
            "recipientPublicKey" : "Public key of recipient account. Required if encrypt == true. String."
            "message" : "Message to send. From 1 to 140 characters. Required",
            "secret" : "Secret passphrase of your account. String. Required",
            "secondSecret": "Second public key if using. String. ",
            "publicKey": "Your public key to verify sender. String, Hex. Optional"
        }

+ Response 200 (application/json)

        {
            "success":true,
            "transactionId": "Id of transaction."
        }

# Group usernames

## Register username [/api/accounts/username]

### Put username [PUT]

+ Request (application/json)

        {
            "secret" : "Secret passphrase of your account. String. Required",
            "secondSecret": "Second public key if using. String. ",
            "publicKey": "Your public key to verify sender. String, Hex. Optional",
            "username" : "Username of delegate. String from 1 to 20 characters."
        }

+ Response 200 (application/json)

        {
            "success":true,
            "transactionId": "Id of transaction."
        }

# Group Contacts

## Add contact [/api/contacts]

### Put contact [PUT]

+ Request (application/json)

        {
            "secret" : "Secret passphrase of your account. String. Required",
            "secondSecret": "Second public key if using. String. ",
            "publicKey": "Your public key to verify sender. String, Hex. Optional",
            "following": "Address or username of account to follow. String. Required"
        }

+ Response 200 (application/json)

        {
            "success":true,
            "transactionId": "Id of transaction."
        }

## Get contacts [/api/contacts/?publicKey={publicKey}]

Get contacts of account by public key

### Get contact [GET]

+ Parameters

    + publicKey (required, string) ... public key of account.

+ Response 200 (application/json)

        {
            "success": true,
            "following": "array of you following accounts",
            "followers": "array of your followers"
        }


## Get unconfirmed contacts [/api/contacts/unconfirmed?publicKey={publicKey}]

Get unconfirmed contacts by public key.

### Get unconfirmed contacts [GET]

+ Parameters

    + publicKey (required, string) ... public key of account.

+ Response 200 (application/json)

        {
            "success": true,
            "contacts": ["array of contacts transactions"]
        }

# Group Dapps

## DApps [/api/dapps]

### Add DApp [PUT]

+ Request (application/json)

        {
            "secret": "Secret of account. String. Required",
            "secondSecret": "Second secret of account. String. Optional",
            "publicKey": "Public key to verify sender secret key. Hex. Optional",
            "category": "Category of DApp. Integer. Required",
            "name": "Name of DApp. String. Required",
            "description": "Description of DApp. String. Optional",
            "tags": "Tags of DApp. String. Optional",
            "type" : "Type of DApp, now supported only 0 type. Integer. Required",
            "siaAscii": "ASCII code of sia shared file. String. Optional",
            "git": "Link to git repository. String. Optional",
            "icon": "Link to icon file. PNG and JPG/JPEG supported. String. Optional",
            "siaIcon": "ASCII code of sia shared icon file. String. Optional"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transactionId": "id of transaction"
        }

## Get Dapps [/api/dapps?category={category}&name={name}&type={type}&git={git}&siaAscii={siaAscii}&siaIcon={siaIcon}&icon={icon}&limit={limit}&offset={offset}&orderBy={orderBy}]

### Get Dapps [GET]

+ Parameters

    + category (optional, integer) ... Category of DApp.
    + name (optional, string) ... Name of DApp.
    + type (optional, integer) ... Type of DApp.
    + git (optional, string) ... Git repository link to DApp.
    + limit (optional, integer) ... Limit of dapps in query. Maximum is 100.
    + offset (optional, integer) ... Offset of dapps in query.
    + orderBy (optional, string) ... Order by field.

+ Response 200 (application/json)

        {
            "success": true,
            "dapps": "Array of dapps"
        }

## Get DApp [/api/dapps/get?id={id}]

### Get DApp [GET]

+ Parameters

   + id (required, optional) ... Id of DApp.

+ Response 200 (application/json)

        {
            "success": true,
            "dapp": "dapp"
        }
## Search [/api/dapps/search?q={q}&category={category}&installed={installed}]

### Search [GET]

+ Parameters

    + q (required, string) ... query to search.
    + category (optional, integer) ... category to search.
    + installed (optional, integer) ... search only in installed dapp. 1 or 0.

+ Response 200 (application/json)

        {
            "success": true,
            "dapps": ["array of dapps"]
        }

## Install DApp [/api/dapps/install]

Will install DApp on your node.

### Install DApp [POST]

+ Request (application/json)

        {
            "id": "id of dapp"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "path": "path of installed dapp"
        }

## Installed DApps [/api/dapps/installed]

Return list of installed dapps.

### Installed DApps [GET]

+ Response 200 (application/json)

        {
            "success": "true",
            "dapps": ["array of dapps"]
        }

## Installed DApps Ids [/api/dapps/installedIds]

Return list of installed dapps ids.

### Installed DApps Ids [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "ids": ["ids of dapps"]
        }

## Uninstall DApp [/api/dapps/uninstall]

Will uninstall dapp from your node.

### Uninstall DApp [POST]

+ Request (application/json)

        {
            "id": "id of dapp"
        }

+ Response 200 (application/json)

        {
            "success": true
        }

## Launch DApp [/api/dapps/launch]

It will launch dapp on your node

### Launch DApp [POST]

+ Request (application/json)

        {
            "id": "id of dapp to launch",
            "params": "params to launch dapp, not required, array"
        }

+ Response 200 (application/json)

        {
            "success": true
        }

## Installing [/api/dapps/installing]

Will return dapps that in installing right now.

### Installing [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "installing": ["ids of dapps that installing"]
        }

## Removing [/api/dapps/removing]

Will return dapps that in uninstalling right now.

### Removing [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "removing": ["ids of dapps that removing"]
        }

## Launched [/api/dapps/launched]

Will return launched dapps.

### Launched [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "launched": ["list of launched dapps ids"]
        }

## Categories [/api/dapps/categories]

Will return categories of dapps.

### Categories [GET]

+ Response 200 (application/json)

        {
            "success": true,
            "category": "object with names and ids of dapps categories"
        }

## Stop DApp [/api/dapps/stop]

Will stop dapp on your node.

### Stop DApp [POST]

+ Request (application/json)

        {
            "id": "id of dapp to stop it"
        }

+ Response 200 (application/json)

        {
            "success": true
        }

## Icon DApp [/api/dapps/icon]

Using to get icon of dapp from sia.

### Icon DApp [GET]

+ Paremeters
    + id (required, string) ... id of dapp

+ Response 200 (application/octet-stream)

# Group Multisignatures

Multisignature group API.

## Get pending multisignatures/transactions [/api/multisignatures/pending?publicKey={publicKey}]

Return multisig transaction that waiting for your signature.

### Get pending multisignatures/transactions [GET]

+ Parameters

    + publicKey (required,string) ... public key of account, hex string.


+ Response 200 (application/json)

        {
            "success": true,
            "transactions": ['array of transactions to sign']
        }

## Create multisignature account [/api/multisignatures]

Create a multisignature account

### Create multisignature account [PUT]

+ Request (application/json)

        {
            "secret": "your secret. string. required.",
            "lifetime": "request lifetime in hours (1-24). required.",
            "min": "minimum signatures needed to approve a tx or a change (1-15). integer. required",
            "keysgroup": [array of public keys strings]. add '+' before publicKey to add an account or '-' to remove. required.
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transactionId": "transaction id"
        }

## Sign transaction [/api/multisignatures/sign]

Sign transaction that wait for your signature.

### Sign transaction [POST]

+ Request (application/json)

        {
            "secret": "your secret. string. required.",
            "publicKey": "public key of your account. string. optional.",
            "transactionId": "id of transaction to sign"
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transactionId": "transaction id"
        }

## Enable multisignature on account [/api/multisignatures]

Enable multisignature on your account.

+ Request (application/json)

        {
            "secret": "your secret. string. required.",
            "publicKey": "public key of your account. string. optional.",
            "secondSecret": "your second secret if you have it. string. optional.",
            "min": "min count of signatures to process transaction. integer. required",
            "lifetime": "life time of transaction. from 1 to 72 hours. integer. required",
            "keysgroup": "array of public keys of other signers."
        }

+ Response 200 (application/json)

        {
            "success": true,
            "transactionId": "transactionId"
        }

## Get accounts of multisignature [/api/multisignatures/accounts?publicKey={publicKey}]

Get accounts of multisignature.

### Get accounts of multisignature [GET]

+ Parameters

    + publicKey (required, string) ... Public key of multisignature account.

+ Response 200 (application/json)

        {
            "success": true,
            "accounts": "array of accounts"
        }
