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
HTTP_METHOD=POST
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI" -d '{"public_keys": ["xpub661MyMwAqRbcFCpjo8ucHDkLWb8Ba9ahwP2jQa7eR8QgX6KXNNXq595uEA785XJS7NReNS1RyRe7zz7KFS6UZqHSwwdWEcnZExoNmo3T1q6"],"label": "mywallet"}'

```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "11",
		"type": "wallet",
		"attributes": {
			"id": 1,
			"label": "mywallet",
			"coin_id": 1,
			"active": true
		}
	}
}
```

This endpoint creates a new wallet.

### HTTP Request

`POST api/v1/:coin_name/wallets`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
public_key | String | Root public key from user | No 
label | String | A name for the wallet | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No


## Show Wallet  

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "11",
		"type": "wallet",
		"attributes": {
			"id": 11,
			"label": "mywallet",
			"coin_id": 1,
			"active": true,
			"balance": {
				"confirmed_balance": 0,
				"unconfirmed_balance": 0,
				"locked_balance": 0
			}
		}
	}
}
```

This endpoint retrieves a specific wallet.


### HTTP Request

`GET api/v1/:coin_name/wallets/:id`

### URL Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
ID | Integer | The ID of the Wallet to retrieve | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No

## List Wallet

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": [{
		"id": "11",
		"type": "wallet",
		"attributes": {
			"id": 11,
			"label": "mywallet",
			"coin_id": 1,
			"active": true
		}
	}]
}
```

This endpoint lists the entire wallets.

### HTTP Request

`GET  api/v1/:coin_name/wallets` 

### URL Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No

# Address

## Create Address

```shell
HTTP_METHOD=POST
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/addresses
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "79",
		"type": "address",
		"attributes": {
			"id": 79,
			"wallet_id": 11,
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ",
			"client_wallet_path": "m/44/0/1/0/79",
			"service_provider_wallet_path": "m/44/0/1/0/79",
			"arbitrator_wallet_path": "m/44/0/1/0/79",
			"change": false
		}
	}
}
```

This endpoint creates a new address.

### HTTP Request

`POST /api/v1/:coin_name/wallets/:id/addresses`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
id | Integer | id of the wallet in which address has to be generated | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No



## Show Address

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/addresses/79
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "79",
		"type": "address",
		"attributes": {
			"id": 79,
			"wallet_id": 11,
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ",
			"client_wallet_path": "m/44/0/1/0/79",
			"service_provider_wallet_path": "m/44/0/1/0/79",
			"arbitrator_wallet_path": "m/44/0/1/0/79",
			"change": false
		}
	}
}
```

This endpoint shows details of an address.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/addresses/:id`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which address has to be generated | No
id | Integer | id of address to fetch | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No



## Latest Address 

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/addresses/latest
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "79",
		"type": "address",
		"attributes": {
			"id": 79,
			"wallet_id": 11,
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ",
			"client_wallet_path": "m/44/0/1/0/79",
			"service_provider_wallet_path": "m/44/0/1/0/79",
			"arbitrator_wallet_path": "m/44/0/1/0/79",
			"change": false
		}
	}
}
```


This endpoint shows details of the latest address generated for that wallet.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/addresses/latest`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which address has to be generated | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No


## List Addresses

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/addresses
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": [{
		"id": "1",
		"type": "address",
		"attributes": {
			"id": 1,
			"wallet_id": 11,
			"address": "2N87yzNRi69sNcMRfMRPiKyzek1vhuea9EG",
			"client_wallet_path": "m/44/0/1/0/1",
			"service_provider_wallet_path": "m/44/0/1/0/1",
			"arbitrator_wallet_path": null,
			"change": false
		}
	}, {
		"id": "79",
		"type": "address",
		"attributes": {
			"id": 79,
			"wallet_id": 11,
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ",
			"client_wallet_path": "m/44/0/1/0/79",
			"service_provider_wallet_path": "m/44/0/1/0/79",
			"arbitrator_wallet_path": "m/44/0/1/0/79",
			"change": false
		}
	}]
}
```


This endpoint lists all addresses generated for that wallet.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/addresses`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which address has to be generated | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No


# Withdrawals

## Create Withdrawal

```shell
HTTP_METHOD=POST
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/withdrawals
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI" -d '{"recipient_address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc","amount": 63750,"fee_per_byte": 10}'  
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "78",
		"type": "withdrawal_create",
		"attributes": {
			"id": 78,
			"wallet_id": 11,
			"amount": 60000,
			"transaction_data": {
				"unsigned_tx": "020000000001010E0EFFF5A9A7190E870B672FEA613B018A4CD18981C68A3B45FCD249DA296A4E00000000232200204FC5F723B5D15ED8ABCFF2E5758BCAF6E41A1744A83592B63DF62469C7DA5A1AFEFFFFFF0360EA00000000000017A914B762ABDE8B64CC2074CCF2759F81AFE5360D6115870F0000000000000017A91444B499EC242F7A587738F5CF598665526A9D5F3487952F30010000000017A914DD8E5B5395250C0ABE330ED09FA3C5D75A6F6810870000000000",
				"sign_parts": [{
					"txid": "4e6a29da49d2fc453b8ac68189d14c8a013b61ea2f670b870e19a7a9f5ff0e0e",
					"vout": "0",
					"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ",
					"amount": "20000000",
					"sign_hash": "9F5F6A356876C9CB4E395F943FF1236B8050A849693E41B0DAA4A5E9E3E6747A",
					"signing_keys": [{
						"child_pub": "03364F18A5373D6FC5FD2E190F4DAF2E493A1290300752547C735EDBE0B02FAEBA",
						"path": "m/44/0/1/0/79"
					}, {
						"child_pub": "026AABA63A18991E796474781DBA159E7153C8BD3219F7B748DF6F8FF597FFC2C4",
						"path": "m/44/0/1/0/79"
					}]
				}]
			}
		}
	}
}
```

This endpoint creates a new withdrawal from the user's wallet.

### HTTP Request

`POST /api/v1/:coin_name/wallets/:wallet_id/withdrawals`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
recipient_address | String | address to which the withdrawal should be made | No
amount | Integer | value of bitcoin in sathoshi's | No
fee_per_byte | Integer | fee ( in sathoshi's ) to be spend per byte of the bitcoin transaction
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No


## Finalize Withdrawal

```shell
HTTP_METHOD=POST
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/withdrawals/78/finalize
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

note: use signed txn data from credence interface as client_signed_txn

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI" -d '{"client_signed_txn": "{\"unsigned_tx\":\"02000000000101B7938D3A068467A4CAAE04871DE84C864B0808D8C4FA8E41F8254E89FEBC7D880100000023220020741FAD10CE1271F53614008BE1D7A69EC5AF10E46158C1881D3510824147DD6DFEFFFFFF0206F900000000000017A91444B499EC242F7A587738F5CF598665526A9D5F34874E1010000000000017A91418282DF53C7E947F4F3E8FC4D62A6C588BBB10F1870000000000\",\"verified\":false,\"sign_parts\":[{\"txid\":\"887dbcfe894e25f8418efac4d808084b864ce81d8704aecaa46784063a8d93b7\",\"vout\":1,\"address\":\"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8\",\"amount\":\"1120000\",\"sign_hash\":\"C7D5D0CCD7FF90DA9A23CEA5B383771574E6B3A870D5B06ED59FF3DCB9869782\",\"signing_keys\":[{\"child_pub\":\"03208251CA155FB6397D99C23148ABCEEFD0A4F7BDAD0CFAF3FBD0E6C9EA40A9F3\",\"path\":\"m/44/0/11/0/51\",\"signature\":\"3045022100F52FB9785081DBB24D260F1D7F4B34A137706145C554B34DAA3B235FDAA21F35022058BC5B0F6D88C436D74D5AD54F0343CF43D727C4F17ACEDB91A904BED355B89401\",\"verified\":false},{\"child_pub\":\"026E29F28DA14804B4895A235409F2E01806402E4C7BA8D8D1285C858C6AADEDC8\",\"path\":\"m/44/0/11/0/51\",\"signature\":null,\"verified\":false},{\"child_pub\":\"02B43CD873507D28BD2F2360E6A7320DC924FD6DE22937915C6E1E49651FEC549F\",\"path\":\"m/44/0/11/0/51\",\"signature\":null,\"verified\":false}]}]}"
    }'
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "78",
		"type": "withdrawal_finalize",
		"attributes": {
			"id": 78,
			"txid": "44f8ea3284b78cd7d4e9e72fa3a7d7a4194738dd943b556561cab386516b88cf",
			"amount": 60000,
			"fee": 4860
		}
	}
}
```

This endpoint creates a new withdrawal from the user's wallet.

### HTTP Request

`POST /api/v1/:coin_name/wallets/:wallet_id/withdrawals/:withdrawal_id/finalize`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
withdrawal_id | Integer | ID of withdrawal to be finalized | No
client_signed_txn | JSON | signed txn data from credence interface | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No



## List Withdrawals

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/withdrawals
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag==
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"  
```

> The above command returns JSON structured like this:

```json
{
	"data": [{
		"id": "78",
		"type": "withdrawal",
		"attributes": {
			"id": 78,
			"wallet_id": 11,
			"txid": "44f8ea3284b78cd7d4e9e72fa3a7d7a4194738dd943b556561cab386516b88cf",
			"recipient_address": "2N9xsrCHy6gRh4QjiRT1NQwLLd9rJKVWHEG",
			"amount": 60000,
			"fee": 4860,
			"block_height": null,
			"block_hash": null,
			"confirmations": null,
			"fee_per_byte": 10,
			"status": "success"
		}
	}]
}
```

This endpoint lists all withdrawal for a specific wallet.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/withdrawals`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No

## Show Withdrawals

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/withdrawals/78
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag==
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"  
```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "78",
		"type": "withdrawal",
		"attributes": {
			"id": 78,
			"wallet_id": 11,
			"txid": "44f8ea3284b78cd7d4e9e72fa3a7d7a4194738dd943b556561cab386516b88cf",
			"recipient_address": "2N9xsrCHy6gRh4QjiRT1NQwLLd9rJKVWHEG",
			"amount": 60000,
			"fee": 4860,
			"block_height": null,
			"block_hash": null,
			"confirmations": null,
			"fee_per_byte": 10,
			"status": "success"
		}
	}
}
```

This endpoint retrieves a specific withdrawal.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/withdrawals/:withdrawal_id`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
withdrawal_id | Integer | ID of withdrawal to be finalized | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No




# Deposits

## List Deposits

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/deposits
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag==
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"
```

> The above command returns JSON structured like this:

```json
{
	"data": [{
		"id": "29",
		"type": "deposit",
		"attributes": {
			"id": 29,
			"wallet_id": 11,
			"txid": "4e6a29da49d2fc453b8ac68189d14c8a013b61ea2f670b870e19a7a9f5ff0e0e",
			"vout": 0,
			"amount": 20000000,
			"block_height": 1449842,
			"block_hash": "00000000000001397c0f01bbcdac235fc14551e1b97cbb5357ea1a10ce49a54a",
			"confirmations": 5,
			"block_time": "2018-12-28T06:31:23.000Z",
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ"
		}
	}]
}
```

This endpoint lists all deposits for a specific wallet.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/deposits`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No



## Show Deposit

```shell
HTTP_METHOD=GET
HOST=https://custodian-staging.coinome.com
URI=/api/v1/bitcoin/wallets/11/deposits/15
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z");curl -H "Content-Type: $CONTENT_TYPE" -H "X_AUTHORIZATION: APIAuth $UUID:$(echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY "$@" | base64)" -H "Date: $DATE" -H "Authorization: Bearer $DOOR_KEEPER_TOKEN" -X $HTTP_METHOD "$HOST$URI"  

```

> The above command returns JSON structured like this:

```json
{
	"data": {
		"id": "29",
		"type": "deposit",
		"attributes": {
			"id": 29,
			"wallet_id": 11,
			"txid": "4e6a29da49d2fc453b8ac68189d14c8a013b61ea2f670b870e19a7a9f5ff0e0e",
			"vout": 0,
			"amount": 20000000,
			"block_height": 1449842,
			"block_hash": "00000000000001397c0f01bbcdac235fc14551e1b97cbb5357ea1a10ce49a54a",
			"confirmations": 5,
			"block_time": "2018-12-28T06:31:23.000Z",
			"address": "2N4mzKaPLDqVw5isPQRAY8kYg41Jw3KvLiJ"
		}
	}
}
```

This endpoint lists all deposits for a specific wallet.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/deposits/:deposit_id`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
deposit_id | Integer | ID of deposit to be retrieved | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No