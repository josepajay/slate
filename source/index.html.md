---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - Shell

toc_footers:
  - <a href='https://custodian-staging.coinome.com/'>Sign Up for Developer Keys</a>
  - <a href='https://hatio.in/'>Powered by Hatio Innovations</a>

includes:
  - errors

search: true
---

# Getting Started

### Overview

Custodian provides a simple and robust REST-ful API as well as a simple client javascript SDK to integrate multi-signature technology into your existing bitcoin applications and services.

The Custodian SDK enables the following operations:

* Creation of P2SH (multi-signature) wallets
* Hierarchical Deterministic Wallet management (BIP32)
* Transaction creation
* Transaction signing
* Multi-signer wallet flow


### Multi-Signature Wallets

The primary advantage of multi-signature wallets is the ability for multiple machines and people to work together to approve a given transaction. Without multiple signatures on a transaction, all credentials to approve a transaction must reside with a single person on a machine. If that person, or machine is compromised by an attacker, all of your bitcoin can be taken.

Traditionally, it has been so difficult to secure these single person / single machine systems, that many vendors have opted to simply use “cold storage” and move Bitcoin offline entirely.

Multi-signature wallets offer all the flexibility you would expect from a modern Bitcoin address without having to take your bitcoin offline. The Custodian API enables you to use multi-signature features in your own applications so you can harness the full flexibility of multiple users, cosigners and state-of-the-art fraud detection services to protect against loss and theft.

### HD Wallets

All Custodian wallets are hierarchical deterministic wallets - also known as “HD Wallets”. HD Wallets are implemented using the bitcoin BIP32 standard. As such, Custodian's HD Wallets are built from ‘keychains’ rather than from individual keys, and offer two distinct security and privacy enhancing features:

* More secure backups

Because keychains can be backed up with a single secret, a wallet can use many public keys all of which are maintained by a single backup key.

* Blockchain Privacy

With HD Wallets, applications can create new keys with every transaction such that no two transactions ever appear to come from the same wallet. This protects the wallet holder from revealing the true size of the wallet.

# User Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "https://custodian-staging.coinome.com/oauth/token"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Custodian uses API keys to allow access to the API. You can register a new Custodian API key at our [developer portal].

Custodian expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Wallets

## Create Wallet


```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
  -X $HTTP_METHOD "$HOST$URI" -d '{"public_key": xpub661MyMwAqRbcFCpjo8ucHDkLWb8Ba9ahwP2jQa7eR8Q",
  "label": "mywallet"}'
```

> The above command returns JSON structured like this:

```json
{ 
    "result": 
  {
    "id": 11,
    "label": "mywallet",
    "coin": "bitcoin",
    "active": true
  }
}
```

This endpoint creates a new wallet.

### HTTP Request

`POST api/v1/bitcoin/wallets`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
public_key | string | Root public key from user | Yes 
label | string | A name for the wallet | Yes


## Show Wallet  

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
  -X $HTTP_METHOD "$HOST$URI"

```

> The above command returns JSON structured like this:

```json
{ 
  "result": 
  { 
    "id":11, 
    "label":"mywallet",
    "coin":"bitcoin",
    "active":true,
    "balance":
    { 
      "confirmed_balance":0,
      "unconfirmed_balance":0
    }
  }
}
```

This endpoint retrieves a specific wallet.


### HTTP Request

`GET api/v1/bitcoin/wallets/:id`

### URL Parameters

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
ID | Integer | The ID of the Wallet to retrieve | Yes

## List Wallet

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
  -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{ 
  "results":
  [
    { 
      "id":11,
      "label":"mywallet",
      "coin":"bitcoin",
      "active":true
    }
  ]
}  
```

This endpoint lists the entire wallets.

### HTTP Request

`GET  api/v1/bitcoin/wallets` 

### URL Parameters

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
 No parameters are required for this api endpoint.

# Address

## Create Address

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" 
  -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
    -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
  "result":
  {
    "id":51,
    "wallet_id":11,
    "address":"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
    "client_wallet_path":"m/44/0/11/0/51",
    "service_provider_wallet_path":"m/44/0/11/0/51",
    "external_wallet_path":"m/44/0/11/0/51",
    "change":false
  }
}
```

This endpoint creates a new address.

### HTTP Request

`POST /api/v1/bitcoin/wallets/:id/addresses`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
id | integer | id of the wallet in which address has to be generated | yes



## Show Address

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" 
  -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
    -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
  "result":
  {
    "id":51,
    "wallet_id":11,
    "address":"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
    "client_wallet_path":"m/44/0/11/0/51",
    "service_provider_wallet_path":"m/44/0/11/0/51",
    "external_wallet_path":"m/44/0/11/0/51",
    "change":false
  }
}
```

This endpoint shows details of an address.

### HTTP Request

`GET /api/v1/bitcoin/wallets/:wallet_id/addresses/:id`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
wallet_id | integer | id of the wallet in which address has to be generated | yes
id | integer | id of address to fetch | yes



## Latest Address 

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" 
  -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
    -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
  "result":
  {
    "id":51,
    "wallet_id":11,
    "address":"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
    "client_wallet_path":"m/44/0/11/0/51",
    "service_provider_wallet_path":"m/44/0/11/0/51",
    "external_wallet_path":"m/44/0/11/0/51",
    "change":false
  }
}
```


This endpoint shows details of the latest address generated for that wallet.

### HTTP Request

`GET /api/v1/bitcoin/wallets/:wallet_id/addresses/latest`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
wallet_id | integer | id of the wallet in which address has to be generated | yes


## List Addresses

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth $UUID" 
  -H "Date: $DATE" 
  -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
    -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
  "result":
  [{
    "id":1,
    "wallet_id":11,
    "address":"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
    "client_wallet_path":"m/44/0/11/0/1",
    "service_provider_wallet_path":"m/44/0/11/0/1",
    "external_wallet_path":"m/44/0/11/0/1",
    "change":false
  },
  {
    "id":2,
    "wallet_id":11,
    "address":"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
    "client_wallet_path":"m/44/0/11/0/2",
    "service_provider_wallet_path":"m/44/0/11/0/2",
    "external_wallet_path":"m/44/0/11/0/2",
    "change":false
  }]
}
```


This endpoint lists all addresses generated for that wallet.

### HTTP Request

`GET /api/v1/bitcoin/wallets/:wallet_id/addresses`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
wallet_id | integer | id of the wallet in which address has to be generated | yes


# Withdrawals

## Create Withdrawal

```shell
curl -H "Content-Type: $CONTENT_TYPE" 
  -H "X_AUTHORIZATION: APIAuth" 
  -H "Date: $DATE" 
  -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" 
  -X $HTTP_METHOD "$HOST$URI" 
  -d '{"recipient_address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
       "amount": 63750,"fee_per_byte": 10}'  
```

> The above command returns JSON structured like this:

```json
{
	"result": {
		"id": 48,
		"wallet_id": 11,
		"amount": 63750,
		"transaction_data": {
			"unsigned_tx": "02000000000101B7938D3A068467A4CAA91418282DF53C7E947F4F3E8FC4D62A6C588BBB10F1870000000000",
			"sign_parts": [{
				"txid": "887dbcfe894e25f8418efac4d808084b864ce81d8704aecaa46784063a8d93b7",
				"vout": "1",
				"address": "2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8",
				"amount": "1120000",
				"sign_hash": "C7D5D0CCD7FF90DA9A23CEA5B383771574E6B3A870D5B06ED59FF3DCB9869782",
				"signing_keys": [{
					"child_pub": "03208251CA155FB6397D99C23148ABCEEFD0A4F7BDAD0CFAF3FBD0E6C9EA40A9F3",
					"path": "m/44/0/11/0/51"
				}, {
					"child_pub": "026E29F28DA14804B4895A235409F2E01806402E4C7BA8D8D1285C858C6AADEDC8",
					"path": "m/44/0/11/0/51"
				}, {
					"child_pub": "02B43CD873507D28BD2F2360E6A7320DC924FD6DE22937915C6E1E49651FEC549F",
					"path": "m/44/0/11/0/51"
				}]
			}]
		}
	}
}
```

This endpoint creates a new withdrawal from the user's wallet.

### HTTP Request

`POST /api/v1/bitcoin/wallets/:wallet_id/withdrawals`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
wallet_id | integer | id of the wallet in which withdrawal has to be generated | yes
recipient_address | string | address to which the withdrawal should be made | yes
amount | integer | value of bitcoin in sathoshi's | yes
fee_per_byte | integer | fee ( in sathoshi's ) to be spend per byte of the bitcoin transaction
