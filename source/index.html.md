---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - Shell

toc_footers:
  - <a href='https://custodian-staging.coinome.com/'>Sign Up for Developer Keys</a>
  - <a href='https://hatio.in/'>Powered by Hatio Innovations</a>

includes:

search: true
---

# Getting Started

## Overview

Enterprise Multi-Signature Wallet is a High Security Solution to store Digital Assets and Crypto Currencies protected by the safety of multiple cryptographic keys and thereby greatly reducing the attack surface of a Single-Key storage. 

All Credence wallets are hierarchical deterministic wallets - also known as “HD Wallets”. HD Wallets are implemented using the bitcoin BIP32 standard. As such, Credence's HD Wallets are built from ‘keychains’ rather than from individual keys. Keychains can be backed up with a single secret, a wallet can use many public keys all of which are maintained by a single backup key.

The primary advantage of multi-signature wallets is the ability for multiple machines and people to work together to approve a given transaction. Without multiple signatures on a transaction, all credentials to approve a transaction must reside with a single person on a machine. If that person, or machine is compromised by an attacker, all of your bitcoin can be taken.

Traditionally, it has been so difficult to secure these single person / single machine systems, that many vendors have opted to simply use “cold storage” and move Bitcoin offline entirely.

Multi-signature wallets offer all the flexibility you would expect from a modern Bitcoin address without having to take your bitcoin offline. The Credence API enables you to use multi-signature features in your own applications so you can harness the full flexibility of multiple users, cosigners and state-of-the-art fraud detection services to protect against loss and theft.


## Account Setup 

```
APP_UID=ca93bb64cc41721ad28e45c0b554bc926dba195e8451d5d12bf17d382b1ee4ee  
APP_SECRET=a9effb7c41e6e2e38b330664b4a64d1aa62287b801317afb70b774c567317797  
REDIRECT=urn:ietf:wg:oauth:2.0:oob  
CODE=5e78e6f17f1692b7b76b5da6b3a6e6fe8b0c469d96a6ca09fc140e9cc870c055  

curl -F grant_type=authorization_code \
-F client_id=$APP_UID \
-F client_secret=$APP_SECRET \
-F code=$CODE \
-F redirect_uri=urn:ietf:wg:oauth:2.0:oob \
-X POST https://custodian-staging.coinome.com/oauth/token
```

> The above command returns response which contain access and refresh tokens:

```json
{
    "access_token": "2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf",
    "token_type": "Bearer",
    "refresh_token": "767377c4e2ba771f431a9bddbb74f8bbf4a47497c7b42ee65137cdcef96430e1",
    "scope": "public",
    "created_at": 1544519236
}
```

Before you begin, you need to set up an enterprise account by signing up at https://accounts-dev.coinome.com. Generate new APP_UID, APP_SECRET and CODE for use in every api call to Enterprise Wallet API.

Login as user, click on applications on developer options tab under user-> profile
click on new application to generate APP_UID and APP_SECRET
click authorize, to reveal CODE

Note: use access_token as DOOR_KEEPER_TOKEN for following requests.

Login as user, click on list hmac auth keys on developer options tab under user-> profile
click on generate, to get Hmac key and Hmac AccessId  

Note: use UUID as Hmac AccessId and SECRET_KEY as Hmac key for following requests.

# Wallet

Before you can safely send or receive crypto assets using our product you need to set up a multi signature wallet.
You will be asked to provide root public keys od BIP32 specification. In a typical 2/3 multi signature setup, User and Enterprise Wallet will hold one root private key. A backup key will be also required in case user looses his primary root private.
	

## Create Wallet


```shell
HTTP_METHOD=POST  
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets  
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e  
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag==  
CONTENT_TYPE=application/json  
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
MD5=tQHGWqadwmQzxvBwCJ1C8w==  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")  
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,$MD5,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`



curl -H "Content-MD5: $MD5" \
-H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI" \
-d '{
    "client_keys": ["xpub661MyMwAqRbcF4ceR3gLXaH4FL48WdnocXxs7pZvwSBkJNMhqRWZA7LUefxjjprdFFhR3nhowFs1fNnQVEMUikxLDUhdY9YMUANyMJGT6Qy"],
    "label": "mywallet",
    "signature_type": "multisig-2-of-3",
    "recovery": false,
    "recovery_keys": ["xpub661MyMwAqRbcEjTKyonWcwzp8mcuZCqBnLDrf3bJ1cGGNxBnHqgvMWCVBNiwtBbDgokowDDtzYBdF1kqYUVDcC4mPXUwKw2MXQMNvNkCxUU"]
}'

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


Public key to be provided while wallet creation can be generated via our tool named Credence. <-- HYPERLINK or via any other root key generation services of his/her personal choice.

If Root public key is generated via Credence, there are two choices by which this can be achived.

  * Credence API for key generation
  * Credence Desktop Application

Key generation using Credence API is explained in [next section](#key-generation)

## Key Generation

> Request:

```shell
curl --request GET \
  --url http://192.168.1.200:5000/credenceapi/v1/rootkey/generate \
  --header 'content-type: application/json'
```

> Response:

```json
{
  "seed": "54D04AF25135CFEC1579656FA8DB725FF86FB19CD33704257DCB020E317E5D6AE0C4A8CC0DDD975B4B3B0450B3F4F114D43173A29F7D72D669CF6D6C1082A3B7",
  "words": "arena valve initial come sugar patch royal never push exile spoon rifle laugh wait stock index math claim awkward forget good gold upon carbon",
  "x_priv": "xprv9s21ZrQH143K4LJe2Ko8XKFX5dFX4XHU6tdvNHptCZZPnxgQjdYHtwwYESjbPegCyqnHBuB4rFieGUHadXBdi7Re3ds7PPmMKJbHwh33TS7",
  "x_pub": "xpub661MyMwAqRbcGpP78ML8tTCFdf61Tz1KU7ZXAgEVku6Nfm1ZHArYSkG25mUmfrXZ2ssqJff9vGQFZ8Z6vywQyHQxMWuVxaQps6MPbge5MZQ"
}
```

This endpoint in Credence creates a new HD root public private key pair. A set of 24 words - called Mnemonic words will be generated which is supposed to be kept safe or remembered by user. These words are equivalent to your private key since private keys can be derived from these set of words. Enterprise Wallet does not store this and cannot recover these words for you at any point.

### HTTP Request

`GET /credenceapi/rootkey/generate`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
nil

## Key Recovery

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/rootkey/generate \
  --header 'content-type: application/json' \
  --data '{"words": "minute lab claw whale tone reunion element blur smoke stone lady habit sponsor food lunar"}'
```

> Response:

```json
{
  "seed": "2542FCF849EFCDE147B6E182FFF3DFCB78DCE7C9F977678B59DA10282F07E41ECA719A64B2A15C386D74ACC2D63FFFD9D79489A65CEEEDF9850197C1FC516131",
  "words": "minute lab claw whale tone reunion element blur smoke stone lady habit sponsor food lunar",
  "x_priv": "xprv9s21ZrQH143K34bSKH9ea65FSBFjVNBkMzU4sRqu9m7H4BwVmq83PbJPYF6oGjiPJa8Zj3RS99cKLSJaeCYbcEd7Haju6GWS8Nd27Nz1DWE",
  "x_pub": "xpub661MyMwAqRbcFYfuRJgewE1yzD6DtpubjDPffpFWi6eFvzGeKNSHwPcsPZYfhFB7jn5YqFSqfvzhQBonFrRikk7JippsSD85icekru1Pb8t"
}
```

This endpoint in Credence recovers public private key pair from the provided mnemonic words. This can be used in case user no longer remembers his private public key pairs. 

This functionality is also available in Credence Desktop application.

### HTTP Request

`POST /credenceapi/rootkey/generate`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
words | string | Mnemonic words from which HD root key is to be generated | No
password | string | BIP39 Passphrase | Yes


## Show Wallet  

```shell
HTTP_METHOD=GET    
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets/1331612684  
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")  
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"

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
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")  
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`  

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI" 

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


## Spending Limits

```shell
HOST=https://custodian-staging.coinome.com   
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
HTTP_METHOD=POST  
URI=/api/v1/settings/spending_limits  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
MD5=
SIGNATURE=

curl "Content-MD5: $MD5" \
-H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI" \
-d '{
    "wallet_id": 2066315408,
    "limits": [{
        "category": "per_transaction",
        "value": 5
    }, {
        "category": "per_hour",
        "value": 10
    }, {
        "category": "per_day",
        "value": 15
    }]
}'  

```

> The above command returns JSON structured like this:

```json
{
    "data": [{
        "id": "1",
        "type": "spending_limit",
        "attributes": {
            "wallet_id": 2066315408,
            "category": "per_transaction",
            "value": 5,
            "active": true,
            "created_at": "2019-02-22T07:32:43.245Z",
            "updated_at": "2019-02-22T09:16:22.503Z"
        }
    }, {
        "id": "2",
        "type": "spending_limit",
        "attributes": {
            "wallet_id": 2066315408,
            "category": "per_hour",
            "value": 10,
            "active": true,
            "created_at": "2019-02-22T07:40:51.272Z",
            "updated_at": "2019-02-22T09:16:22.518Z"
        }
    }, {
        "id": "3",
        "type": "spending_limit",
        "attributes": {
            "wallet_id": 2066315408,
            "category": "per_day",
            "value": 15,
            "active": true,
            "created_at": "2019-02-22T09:16:22.537Z",
            "updated_at": "2019-02-22T09:16:22.537Z"
        }
    }]
}  
```

Users of Enterprise Wallet can set various limits on their wallet for added security and control.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/withdrawals/:withdrawal_id`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
limits | [limit] | An array of one or more limit objects. Detailed description of input is given below | No

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
category | string | Limit can be set for per_transaction, per_day, per_hour | No
value | integer | Value of currency. Eg: To add a limit of 2 BTC per day, value should be 2 | No



#Address

Addresses can be created against a wallet(which is explained in the previous section). API's related to address are explained below.

## Create Address

```shell
HTTP_METHOD=POST    
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets/1331612684/addresses  
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"
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

This endpoint creates a new address under the wallet provied.

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
URI=/api/v1/bitcoin/wallets/1331612684/addresses/2   
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")  
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"

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
URI=/api/v1/bitcoin/wallets/1331612684/addresses/latest   
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"

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
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"

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



# Deposits

Users can receive funds after setting up wallet and address.  

## List Deposits

```shell
HTTP_METHOD=GET    
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets/1331612684/deposits    
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag==  
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"

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
URI=/api/v1/bitcoin/wallets/1331612684/deposits/1    
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"
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

This endpoint returns a specific deposit details.

### HTTP Request

`GET /api/v1/:coin_name/wallets/:wallet_id/deposits/:deposit_id`

### Query Parameters

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
wallet_id | Integer | id of the wallet in which withdrawal has to be generated | No
deposit_id | Integer | ID of deposit to be retrieved | No
coin_name | String | Name of the coin in custodian supported coins. eg: bitcoin | No


# Withdrawals

Users can withdraw crypto assets securely using Enterprise Wallet. Transactions go through only when the signatures of both user and Enterprise Wallet are present in the transaction.

## Create Withdrawal

```shell
HTTP_METHOD=POST    
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets/11/withdrawals    
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
MD5=
SIGNATURE=`echo -n "$HTTP_METHOD,$CONTENT_TYPE,$MD5,$URI,$DATE" | openssl dgst -sha1 -binary -hmac $SECRET_KEY | base64`

curl -H "Content-MD5: $MD5" \
-H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI" \
-d '{
    "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
    "amount": 63750,
    "fee_per_byte": 10
}'  

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


This api response contains transaction_data which will be used as an input to [sign transaction api](#sign-transaction) in Credence. (To sign transaction on behalf of user, using his private key)  

## Sign Transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/bitcoin/transaction/sign \
  --header 'content-type: application/json' \
  --data '{
  "unsigned_tx": "020000000001017E3DEB8EF3D28C94D7CB28C9E5988A93E05445578CDCA24C425D8C9691CD5CAF0100000023220020D1D2A8F19DFA12223B5B2F4C842DC033678EED6BAC8F284361E40ED90AAC6668FEFFFFFF02881300000000000017A914EA7424896B39239C97994EDF192A7D0766A9515D87300F11000000000017A9149DB50405A7DC6178F1AFFDE474E18C2D156CCA41870000000000",
  "x_priv": "xprv9s21ZrQH143K42NeaXRcakg3b9ZGMXEEiBC3VbjxFxabEj4g2yMsE98uRpVP3zkdA6F6Nrz8yfXNZRxnWqZw8ckPJokEyzAgv6jEz3Ff7GS",
  "sign_parts": [
    {
      "txid": "",
      "vout": 0,
      "address": "",
      "amount": "",
      "sign_hash": "185c0be5263dce5b4bb50a047973c1b6272bfbd0103a89444597dc40b248ee7c",
      "signing_keys": [
        {
          "child_pub": "032fd4a01886eda61253c2038e22fa1f6cad2a1a0b407795c4712c01703581ff31",
          "path": "m/0/0/0"
        },
       {
          "child_pub": "032fd4a01886eda61253c2038e22fa1f6cad2a1a0b407795c4712c01703581ff31",
          "path": "m/0/0/0"
        }
      ]
    }
  ]
}'
```

> Response:

```json
{
  "unsigned_tx": "020000000001017E3DEB8EF3D28C94D7CB28C9E5988A93E05445578CDCA24C425D8C9691CD5CAF0100000023220020D1D2A8F19DFA12223B5B2F4C842DC033678EED6BAC8F284361E40ED90AAC6668FEFFFFFF02881300000000000017A914EA7424896B39239C97994EDF192A7D0766A9515D87300F11000000000017A9149DB50405A7DC6178F1AFFDE474E18C2D156CCA41870000000000",
  "verified": false,
  "sign_parts": [
    {
      "txid": "",
      "vout": 0,
      "address": "",
      "amount": "",
      "sign_hash": "185c0be5263dce5b4bb50a047973c1b6272bfbd0103a89444597dc40b248ee7c",
      "signing_keys": [
        {
          "child_pub": "032fd4a01886eda61253c2038e22fa1f6cad2a1a0b407795c4712c01703581ff31",
          "path": "m/0/0/0",
          "signature": "304402204F26B272ACDDDA6F9A848D453A202AABC3942A8758034CB9BCFE2C78BC242DD3022009938278FE2C477D99F7D0AE0EAF8861CB88F7C799E294DAD4DD71FDD67D816F01",
          "verified": false
        },
        {
          "child_pub": "032fd4a01886eda61253c2038e22fa1f6cad2a1a0b407795c4712c01703581ff31",
          "path": "m/0/0/0",
          "signature": "304402204F26B272ACDDDA6F9A848D453A202AABC3942A8758034CB9BCFE2C78BC242DD3022009938278FE2C477D99F7D0AE0EAF8861CB88F7C799E294DAD4DD71FDD67D816F01",
          "verified": false
        }
      ]
    }
  ]
}
```

This endpoint in Credence, sign the transaction using the root private key provided. 

After creating a transaction in Enterprise Wallet, it creates a raw transaction without signatures. If user has a 2/3 configuration multi signature wallet, two signatures are required for the transaction to be sucessfully transamitted to blockchain. Out of the three keys, Enterprise Wallet system holds one of them and the rest is owned by user. User can sign the transaction created using his private key via Credence Api. This action can also be performed using Credence desktop application.

### HTTP Request

`POST /credenceapi/v1/:coin_name/transaction/sign`

### Query Parameters

In the response of transaction create API in Enterprise Wallet, a key named transaction_data is present. Private key of the user merged with this transaction_data creates the parameters for Credence transaction sign.

Parameter | Type | Description | Optional
--------- | ---- | ----------- | --------
coin_name | String | Name of the coin | No 
x_priv | String | Root private key | No
unsigned_tx | String | Raw transaction hex, a value in output of transaction create | No
sign_parts | [sign_part] | An array of sign parts. | No

## Finalize Withdrawal

```shell
HTTP_METHOD=POST    
HOST=https://custodian-staging.coinome.com  
URI=/api/v1/bitcoin/wallets/11/withdrawals/78/finalize    
UUID=78c28d6b-7d32-4027-a408-5ec3909abc5e   
SECRET_KEY=zblh3YsDEPlrcGYt1tx1J7Y0q6aPaDrn3oZ77Enf8BCLCDXVjy+t0gf2S2LRacaYDJskyJKeFKVude2TTro3Ag== 
CONTENT_TYPE=application/json   
DOOR_KEEPER_TOKEN=2c6bbada11f6c21738deea51c215ba664d6f6a4a707e4e5fdc10161ee2bb6abf  
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
MD5=
SIGNATURE=

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI" \
-d '{
    "client_signed_txn": "{\"unsigned_tx\":\"02000000000101B7938D3A068467A4CAAE04871DE84C864B0808D8C4FA8E41F8254E89FEBC7D880100000023220020741FAD10CE1271F53614008BE1D7A69EC5AF10E46158C1881D3510824147DD6DFEFFFFFF0206F900000000000017A91444B499EC242F7A587738F5CF598665526A9D5F34874E1010000000000017A91418282DF53C7E947F4F3E8FC4D62A6C588BBB10F1870000000000\",\"verified\":false,\"sign_parts\":[{\"txid\":\"887dbcfe894e25f8418efac4d808084b864ce81d8704aecaa46784063a8d93b7\",\"vout\":1,\"address\":\"2N3ptgmvmUVywNH7RDm5yu5BzquD9GdQtL8\",\"amount\":\"1120000\",\"sign_hash\":\"C7D5D0CCD7FF90DA9A23CEA5B383771574E6B3A870D5B06ED59FF3DCB9869782\",\"signing_keys\":[{\"child_pub\":\"03208251CA155FB6397D99C23148ABCEEFD0A4F7BDAD0CFAF3FBD0E6C9EA40A9F3\",\"path\":\"m/44/0/11/0/51\",\"signature\":\"3045022100F52FB9785081DBB24D260F1D7F4B34A137706145C554B34DAA3B235FDAA21F35022058BC5B0F6D88C436D74D5AD54F0343CF43D727C4F17ACEDB91A904BED355B89401\",\"verified\":false},{\"child_pub\":\"026E29F28DA14804B4895A235409F2E01806402E4C7BA8D8D1285C858C6AADEDC8\",\"path\":\"m/44/0/11/0/51\",\"signature\":null,\"verified\":false},{\"child_pub\":\"02B43CD873507D28BD2F2360E6A7320DC924FD6DE22937915C6E1E49651FEC549F\",\"path\":\"m/44/0/11/0/51\",\"signature\":null,\"verified\":false}]}]}"
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
client_signed_txn | JSON | Response of [sign transaction api](#sign-transaction) from Credence | No
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

DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"  

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
DATE=$(TZ=GMT date "+%a, %d %b %Y %T %Z")
SIGNATURE=

curl -H "Content-Type: $CONTENT_TYPE" \
-H "X_AUTHORIZATION: APIAuth $UUID:$SIGNATURE" \
-H "Date: $DATE" \
-H "Authorization: Bearer $DOOR_KEEPER_TOKEN" \
-X $HTTP_METHOD "$HOST$URI"  
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