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

### What is an HD Wallet ?

All Credence wallets are hierarchical deterministic wallets - also known as “HD Wallets”. HD Wallets are implemented using the bitcoin BIP32 standard. As such, Credence's HD Wallets are built from ‘keychains’ rather than from individual keys, and offer two distinct security and privacy enhancing features:

* More secure backups

Because keychains can be backed up with a single secret, a wallet can use many public keys all of which are maintained by a single backup key.

* Blockchain Privacy

With HD Wallets, applications can create new keys with every transaction such that no two transactions ever appear to come from the same wallet. This protects the wallet holder from revealing the true size of the wallet.


### What does mMulti-Signature mean ?

The primary advantage of multi-signature wallets is the ability for multiple machines and people to work together to approve a given transaction. Without multiple signatures on a transaction, all credentials to approve a transaction must reside with a single person on a machine. If that person, or machine is compromised by an attacker, all of your bitcoin can be taken.

Traditionally, it has been so difficult to secure these single person / single machine systems, that many vendors have opted to simply use “cold storage” and move Bitcoin offline entirely.

Multi-signature wallets offer all the flexibility you would expect from a modern Bitcoin address without having to take your bitcoin offline. The Credence API enables you to use multi-signature features in your own applications so you can harness the full flexibility of multiple users, cosigners and state-of-the-art fraud detection services to protect against loss and theft.

### Overview

Credence provides a simple and robust API to integrate multi-signature technology into your existing bitcoin applications and services.
Credence also provides simple web interfaces for key generation and transaction signing.

The Credence enables the following operations:

* HD root key generation 
* Hierarchical Deterministic key derivation 
* Root public key validation
* Transaction creation
* Transaction signing
* Transaction packing

# HD Keys

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

This endpoint creates a new HD root public private key pair

### HTTP Request

`GET /credenceapi/rootkey/generate`

### Query Parameters

Parameter | type | Description | Required
--------- | ---- | ----------- | --------
nil



## Key Generation (providing mnemonic words and password)

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

This endpoint creates a new HD root public private key pair from the provided mnemonic words and password

### HTTP Request

`POST /credenceapi/rootkey/generate`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
words | string | Mnemonic words from which HD root key is to be generated | no
password | string | BIP39 Passphrase | yes


## Key Derivation

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/ckd/publickey \
  --header 'content-type: application/json' \
  --data '"{\r\n\t\"path\": \"m\/0\",\r\n\t\"x_pub\": \"xpub661MyMwAqRbcGWT7gYxcwtcn9BPkkyx65Q7eHz9ZpJ7a7XPpaWg7mwTPH7fg4iLGVCQxMavZdUS1K4gxeny821orD94DmGhzrRMdqJZuE3L\"\r\n}"'
```

> Response:

```json
{
  "child_pub": "0380C25D30A63A758A1038B29CA939B80B223E0ED46C207892AB087730739151C3"
}
```


This endpoint derives a child public key at the provided derivation path

### HTTP Request

`POST /credenceapi/ckd/publickey`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
x_pub | string | Root public key from which the new child at a specific path is to be derived | no
uncompressed | boolean | It is an uncompressed public key| yes (default: false)

# Bitcoin

## Create Bitcoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/bitcoin/transaction/create \
  --header 'content-type: application/json' \
  --data '{"output":[{"address":"2NEcuEujgZm5scvQRamgevwipwAy1hBra7q","amount":5000},{"address":"2N7d6vbNbN4GrgaesXQ9TvVSrLLGxmGqCGN","amount":1118000}],"input":[{"vout":1,"txid":"af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e","address":"2NEcuEujgZm5scvQRamgevwipwAy1hBra7q","amount":1126500,"redeemscript":"522103d74a4a9f68cbdb5e1d50122a724e91684fba84e69e71f896a6040a8ac49dffa821021027b084c517102657fb5b64d48d1191205fe720c65d3063bb5087cad850abb62102cf64ede349777df5d274c9c74346c9f6f879109a8e455660ab64cde5f31e19c553ae","signing_keys":[{"child_pub":"03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8","path":"m/44/0/5/1/32"},{"child_pub":"021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6","path":"m/44/0/5/1/32"},{"child_pub":"02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5","path":"m/44/0/5/1/32"}]}]}'
```

> Request params expanded form: 

```json
{
  "output": [
    {
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": 5000
    },
    {
      "address": "2N7d6vbNbN4GrgaesXQ9TvVSrLLGxmGqCGN",
      "amount": 1118000
    }
  ],
  "input": [
    {
      "vout": 1,
      "txid": "af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e",
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": 1126500,
      "redeemscript": "522103d74a4a9f68cbdb5e1d50122a724e91684fba84e69e71f896a6040a8ac49dffa821021027b084c517102657fb5b64d48d1191205fe720c65d3063bb5087cad850abb62102cf64ede349777df5d274c9c74346c9f6f879109a8e455660ab64cde5f31e19c553ae",
      "signing_keys": [
        {
          "child_pub": "03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5",
          "path": "m/44/0/5/1/32"
        }
      ]
    }
  ]
}
```

> Response:

```json
{
  "unsigned_tx": "020000000001017E3DEB8EF3D28C94D7CB28C9E5988A93E05445578CDCA24C425D8C9691CD5CAF0100000023220020D1D2A8F19DFA12223B5B2F4C842DC033678EED6BAC8F284361E40ED90AAC6668FEFFFFFF02881300000000000017A914EA7424896B39239C97994EDF192A7D0766A9515D87300F11000000000017A9149DB50405A7DC6178F1AFFDE474E18C2D156CCA41870000000000",
  "sign_parts": [
    {
      "txid": "af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e",
      "vout": "1",
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": "1126500",
      "sign_hash": "384F39B2DBE9D345747EB77E505537A670C79D22C9B662167BE06EDCB2551B89",
      "signing_keys": [
        {
          "child_pub": "03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5",
          "path": "m/44/0/5/1/32"
        }
      ]
    }
  ]
}
```

This endpoint creates a new raw transaction from the provided UTXO's.

### HTTP Request

`POST /credenceapi/v1/bitcoin/transaction/create`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
output | [[output]](#bitcoin-output) | An array of outputs. Detailed description of output is given below | No
input | [[inputs]](#bitcoin-input) | An array of inputs. Detailed description of input is given below | No


### Bitcoin Output

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
address | string | Bitcoin address to which funds are to be received | No
amount | integer | Value of bitcoin to send in sathoshis | No

### Bitcoin Input

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
address | string | Bitcoin address to which funds are to be received | No
amount | integer | Value of bitcoin to send in sathoshis | No
vout | integer | A single Bitcoin transaction can have many outputs. The vout field specifies which output you want to spend | No
txid | string | transaction id of the utxo | No
redeemscript | string | redeemscript of the multisig address | No
signing_keys | [[signing_keys]](#bitcoin-signing-key) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Bitcoin Signing Key

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No


## Sign Bitcoin transaction

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

> Request params expanded form:

```json
{
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
}
```

```json
{
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
}
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

This endpoint sign the transaction using the root private key provided.

### HTTP Request

`POST /credenceapi/v1/bitcoin/transaction/sign`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
x_priv | string | Root private key | No
sign_parts | [[sign_part]](#bitcoin-sign-parts) | An array of sign parts. Detailed description of sign parts is given below | No

### Bitcoin Sign Parts 

Sign Parts is present in output of transaction create api. Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single Bitcoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | Bitcoin address to which funds are to be received | No
amount | integer | Value of bitcoin to send in sathoshis | No
sign_hash | string | a hash of the data to be signed | No
signing_keys | [[signing_key]](#Bitcoin-signing-key-for-transaction-sign) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Bitcoin Signing Key for transaction sign

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No


## Verify Bitcoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/bitcoin/transaction/verify \
  --header 'content-type: application/json' \
  --data '{"unsigned_tx":"020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000","verified":false,"sign_parts":[{"txid":"7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b","vout":0,"address":"2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc","amount":"1180000","sign_hash":"58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099","signing_keys":[{"child_pub":"022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882","path":"m/44/0/3/0/3","signature":"304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801","verified":false},{"child_pub":"03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091","path":"m/44/0/3/0/3","signature":null,"verified":false},{"child_pub":"035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D","path":"m/44/0/3/0/3","signature":null,"verified":false}]}]}'
```

> Request params expanded form

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "verified": false,
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": "1180000",
      "sign_hash": "58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099",
      "signing_keys": [
        {
          "child_pub": "022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882",
          "path": "m/44/0/3/0/3",
          "signature": "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801",
          "verified": false
        },
        {
          "child_pub": "03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        },
        {
          "child_pub": "035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        }
      ]
    }
  ]
}
```

> Response:

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "verified": false,
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": "1180000",
      "sign_hash": "58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099",
      "signing_keys": [
        {
          "child_pub": "022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882",
          "path": "m/44/0/3/0/3",
          "signature": "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801",
          "verified": true
        },
        {
          "child_pub": "03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        },
        {
          "child_pub": "035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        }
      ]
    }
  ]
}
```

This endpoint verifies the signatures in the transaction.

### HTTP Request

`POST /credenceapi/v1/bitcoin/transaction/verify`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
sign_parts | [[sign_part]](#bitcoin-sign-parts-for-transaction-verify) | An array of sign parts. Detailed description of sign parts is given below | No

### Bitcoin Sign Parts for transaction verify

Sign Parts is present in output of transaction create api. Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single Bitcoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | Bitcoin address to which funds are to be received | No
amount | integer | Value of bitcoin to send in sathoshis | No
sign_hash | string | a hash of the data to be signed | No
signing_keys | [[signing_key]](#bitcoin-signing-key-for-transaction-verify) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Bitcoin Signing Key for transaction verify

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No
signature | string | Signature hex generated using the credence sign api | No
verified | boolean | bool value indicating whether the signature is genuine | Yes



## Pack Bitcoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/bitcoin/transaction/pack \
  --header 'content-type: application/json' \
  --data '{"unsigned_tx":"020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000","sign_parts":[{"txid":"7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b","vout":0,"address":"2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc","amount":1180000,"signatures":["304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8","3045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639"],"redeemscript":"5221022b05ddc44d26ca9985978aa00d494eeedb49fb723faa2b30e7590e9b8c6bd8822103fdb02757226e4027c43d06df62c3f19c2d4dea802f743b1a35bec03c9aeff09121035d48fc4c577bf60e6fb46b00bd7d1759ad1020a5c25fba93a54a01dee3f1270d53ae"}]}'
```
> Request params expanded form:

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": 1180000,
      "signatures": [
        "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8",
        "3045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639"
      ],
      "redeemscript": "5221022b05ddc44d26ca9985978aa00d494eeedb49fb723faa2b30e7590e9b8c6bd8822103fdb02757226e4027c43d06df62c3f19c2d4dea802f743b1a35bec03c9aeff09121035d48fc4c577bf60e6fb46b00bd7d1759ad1020a5c25fba93a54a01dee3f1270d53ae"
    }
  ]
}
```

> Response:

```json
{
  "signed_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD87040046304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8473045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639695221022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD8822103FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF09121035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D53AE00000000",
  "verified": false
}
```

This endpoint packs the transaction for broadcasting to blockchain.

### HTTP Request

`POST /credenceapi/v1/bitcoin/transaction/pack`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
sign_parts | [[sign_part]](#bitcoin-sign-parts-for-transaction-pack) | An array of sign parts. Detailed description of sign parts is given below | No

### Bitcoin Sign Parts for transaction pack

Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single Bitcoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | Bitcoin address to which funds are to be received | No
amount | integer | Value of bitcoin to send in sathoshis | No
signatures | [signature] | An array of signatures.



# Litecoin

## Create Litecoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/litecoin/transaction/create \
  --header 'content-type: application/json' \
  --data '{"output":[{"address":"2NEcuEujgZm5scvQRamgevwipwAy1hBra7q","amount":5000},{"address":"2N7d6vbNbN4GrgaesXQ9TvVSrLLGxmGqCGN","amount":1118000}],"input":[{"vout":1,"txid":"af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e","address":"2NEcuEujgZm5scvQRamgevwipwAy1hBra7q","amount":1126500,"redeemscript":"522103d74a4a9f68cbdb5e1d50122a724e91684fba84e69e71f896a6040a8ac49dffa821021027b084c517102657fb5b64d48d1191205fe720c65d3063bb5087cad850abb62102cf64ede349777df5d274c9c74346c9f6f879109a8e455660ab64cde5f31e19c553ae","signing_keys":[{"child_pub":"03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8","path":"m/44/0/5/1/32"},{"child_pub":"021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6","path":"m/44/0/5/1/32"},{"child_pub":"02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5","path":"m/44/0/5/1/32"}]}]}'
```

> Request params expanded form: 

```json
{
  "output": [
    {
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": 5000
    },
    {
      "address": "2N7d6vbNbN4GrgaesXQ9TvVSrLLGxmGqCGN",
      "amount": 1118000
    }
  ],
  "input": [
    {
      "vout": 1,
      "txid": "af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e",
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": 1126500,
      "redeemscript": "522103d74a4a9f68cbdb5e1d50122a724e91684fba84e69e71f896a6040a8ac49dffa821021027b084c517102657fb5b64d48d1191205fe720c65d3063bb5087cad850abb62102cf64ede349777df5d274c9c74346c9f6f879109a8e455660ab64cde5f31e19c553ae",
      "signing_keys": [
        {
          "child_pub": "03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5",
          "path": "m/44/0/5/1/32"
        }
      ]
    }
  ]
}
```

> Response:

```json
{
  "unsigned_tx": "020000000001017E3DEB8EF3D28C94D7CB28C9E5988A93E05445578CDCA24C425D8C9691CD5CAF0100000023220020D1D2A8F19DFA12223B5B2F4C842DC033678EED6BAC8F284361E40ED90AAC6668FEFFFFFF02881300000000000017A914EA7424896B39239C97994EDF192A7D0766A9515D87300F11000000000017A9149DB50405A7DC6178F1AFFDE474E18C2D156CCA41870000000000",
  "sign_parts": [
    {
      "txid": "af5ccd91968c5d424ca2dc8c574554e0938a98e5c928cbd7948cd2f38eeb3d7e",
      "vout": "1",
      "address": "2NEcuEujgZm5scvQRamgevwipwAy1hBra7q",
      "amount": "1126500",
      "sign_hash": "384F39B2DBE9D345747EB77E505537A670C79D22C9B662167BE06EDCB2551B89",
      "signing_keys": [
        {
          "child_pub": "03D74A4A9F68CBDB5E1D50122A724E91684FBA84E69E71F896A6040A8AC49DFFA8",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "021027B084C517102657FB5B64D48D1191205FE720C65D3063BB5087CAD850ABB6",
          "path": "m/44/0/5/1/32"
        },
        {
          "child_pub": "02CF64EDE349777DF5D274C9C74346C9F6F879109A8E455660AB64CDE5F31E19C5",
          "path": "m/44/0/5/1/32"
        }
      ]
    }
  ]
}
```

This endpoint creates a new raw transaction from the provided UTXO's.

### HTTP Request

`POST /credenceapi/v1/litecoin/transaction/create`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
output | [[output]](#litecoin-output) | An array of outputs. Detailed description of output is given below | No
input | [[inputs]](#litecoin-input) | An array of inputs. Detailed description of input is given below | No


### Litecoin Output

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
address | string | litecoin address to which funds are to be received | No
amount | integer | Value of litecoin to send in sathoshis | No

### Litecoin Input

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
address | string | litecoin address to which funds are to be received | No
amount | integer | Value of litecoin to send in sathoshis | No
vout | integer | A single litecoin transaction can have many outputs. The vout field specifies which output you want to spend | No
txid | string | transaction id of the utxo | No
redeemscript | string | redeemscript of the multisig address | No
signing_keys | [[signing_keys]](#litecoin-signing-key) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Litecoin Signing Key

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No


## Sign Litecoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/litecoin/transaction/sign \
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

> Request params expanded form:

```json
{
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
}
```

```json
{
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
}
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

This endpoint sign the transaction using the root private key provided.

### HTTP Request

`POST /credenceapi/v1/litecoin/transaction/sign`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
x_priv | string | Root private key | No
sign_parts | [[sign_part]](#litecoin-sign-parts) | An array of sign parts. Detailed description of sign parts is given below | No

### Litecoin Sign Parts 

Sign Parts is present in output of transaction create api. Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single litecoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | litecoin address to which funds are to be received | No
amount | integer | Value of litecoin to send in sathoshis | No
sign_hash | string | a hash of the data to be signed | No
signing_keys | [[signing_key]](#litecoin-signing-key-for-transaction-sign) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Litecoin Signing Key for transaction sign

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No


## Verify Litecoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/litecoin/transaction/verify \
  --header 'content-type: application/json' \
  --data '{"unsigned_tx":"020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000","verified":false,"sign_parts":[{"txid":"7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b","vout":0,"address":"2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc","amount":"1180000","sign_hash":"58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099","signing_keys":[{"child_pub":"022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882","path":"m/44/0/3/0/3","signature":"304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801","verified":false},{"child_pub":"03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091","path":"m/44/0/3/0/3","signature":null,"verified":false},{"child_pub":"035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D","path":"m/44/0/3/0/3","signature":null,"verified":false}]}]}'
```

> Request params expanded form

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "verified": false,
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": "1180000",
      "sign_hash": "58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099",
      "signing_keys": [
        {
          "child_pub": "022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882",
          "path": "m/44/0/3/0/3",
          "signature": "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801",
          "verified": false
        },
        {
          "child_pub": "03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        },
        {
          "child_pub": "035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        }
      ]
    }
  ]
}
```

> Response:

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "verified": false,
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": "1180000",
      "sign_hash": "58C1FEFE8185B1A9BD5CF1DBFABA25251A80DC7DC537F90DBC060600E3A70099",
      "signing_keys": [
        {
          "child_pub": "022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD882",
          "path": "m/44/0/3/0/3",
          "signature": "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD801",
          "verified": true
        },
        {
          "child_pub": "03FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF091",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        },
        {
          "child_pub": "035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D",
          "path": "m/44/0/3/0/3",
          "signature": null,
          "verified": false
        }
      ]
    }
  ]
}
```

This endpoint verifies the signatures in the transaction.

### HTTP Request

`POST /credenceapi/v1/litecoin/transaction/verify`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
sign_parts | [[sign_part]](#litecoin-sign-parts-for-transaction-verify) | An array of sign parts. Detailed description of sign parts is given below | No

### Litecoin Sign Parts for transaction verify

Sign Parts is present in output of transaction create api. Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single litecoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | litecoin address to which funds are to be received | No
amount | integer | Value of litecoin to send in sathoshis | No
sign_hash | string | a hash of the data to be signed | No
signing_keys | [[signing_key]](#litecoin-signing-key-for-transaction-verify) | An array of signing_keys. Detailed description of signing_keys is given below | No

### Litecoin Signing Key for transaction verify

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
path | string | BIP32 Derivation Path | no
child_pub | string | child public key at the provided derivation path | No
signature | string | Signature hex generated using the credence sign api | No
verified | boolean | bool value indicating whether the signature is genuine | Yes



## Pack Litecoin transaction

> Request:

```shell
curl --request POST \
  --url http://192.168.1.200:5000/credenceapi/v1/litecoin/transaction/pack \
  --header 'content-type: application/json' \
  --data '{"unsigned_tx":"020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000","sign_parts":[{"txid":"7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b","vout":0,"address":"2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc","amount":1180000,"signatures":["304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8","3045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639"],"redeemscript":"5221022b05ddc44d26ca9985978aa00d494eeedb49fb723faa2b30e7590e9b8c6bd8822103fdb02757226e4027c43d06df62c3f19c2d4dea802f743b1a35bec03c9aeff09121035d48fc4c577bf60e6fb46b00bd7d1759ad1020a5c25fba93a54a01dee3f1270d53ae"}]}'
```
> Request params expanded form:

```json
{
  "unsigned_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD870000000000",
  "sign_parts": [
    {
      "txid": "7d529195427b4b3dd03060341688d44ec7f49f8801255312bc7237a1f15f961b",
      "vout": 0,
      "address": "2MyWWHiwWhNTABASboypdDrAdqfXw8GKbKc",
      "amount": 1180000,
      "signatures": [
        "304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8",
        "3045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639"
      ],
      "redeemscript": "5221022b05ddc44d26ca9985978aa00d494eeedb49fb723faa2b30e7590e9b8c6bd8822103fdb02757226e4027c43d06df62c3f19c2d4dea802f743b1a35bec03c9aeff09121035d48fc4c577bf60e6fb46b00bd7d1759ad1020a5c25fba93a54a01dee3f1270d53ae"
    }
  ]
}
```

> Response:

```json
{
  "signed_tx": "020000000001011B965FF1A13772BC12532501889FF4C74ED48816346030D03D4B7B429591527D0000000023220020B856A5625ECD4B3936429E40B17699D9A027179AC76A57A6735251B24F98A220FEFFFFFF02F04902000000000017A914C9A38D2C82C5ADE7BCB59B038AE581F0904310B487EEA20F000000000017A914510685731D87E01EAB05A74AAEF7317537872FDD87040046304402203D75C815B653BFB77E813C0B01782A045121D5B9EB645805BD4379163AC905AD0220553D1A55306D147C9BF8A70A08ECEA81B65EC88E62844BA6CB2C956279269CD8473045022100D01DD4BE2DBC232215C6B64EC18705B7D1C1063E7997C38D60D64EA61CFF715C02206085E7A64E63D38DFC912841010967972DD912B2153816AB070EF69569D2D639695221022B05DDC44D26CA9985978AA00D494EEEDB49FB723FAA2B30E7590E9B8C6BD8822103FDB02757226E4027C43D06DF62C3F19C2D4DEA802F743B1A35BEC03C9AEFF09121035D48FC4C577BF60E6FB46B00BD7D1759AD1020A5C25FBA93A54A01DEE3F1270D53AE00000000",
  "verified": false
}
```

This endpoint packs the transaction for broadcasting to blockchain.

### HTTP Request

`POST /credenceapi/v1/litecoin/transaction/pack`

### Query Parameters

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
unsigned_tx | string | Raw transaction hex, a value in output of transaction create | No
sign_parts | [[sign_part]](#litecoin-sign-parts-for-transaction-pack) | An array of sign parts. Detailed description of sign parts is given below | No

### Litecoin Sign Parts for transaction pack

Description of keys present in sign_parts is given below

Parameter | type | Description | Optional
--------- | ---- | ----------- | --------
txid | string | transaction id of the utxo | No
vout | integer | A single litecoin transaction can have many outputs. The vout field specifies which output you want to spend | No
address | string | litecoin address to which funds are to be received | No
amount | integer | Value of litecoin to send in sathoshis | No
signatures | [signature] | An array of signatures.


# Interactive Console

Interactive console of credence application provides limited functionality as of now. 
Features available through interactive console are: 

* HD root key generation 
* Transaction Signing

Interactive console can be accessed via a web browser at 

* base_url/Sign 
* base_url/RootKeyFunctions

