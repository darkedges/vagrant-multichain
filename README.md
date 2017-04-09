# vagrant-multichain

issue the `vagrant up` command and it should do all the heavy lifting.

If that fails try
`ansible-playbook -i static_inventory provision-multichain.yml  -u vagrant`

You should be able to follow from step 3 at http://www.multichain.com/getting-started/

## Connecting

In the project directory use `ssh` with any of the following 

```bash
ssh -i roles/multichain/files/id_rsa root@192.168.50.201
ssh -i roles/multichain/files/id_rsa root@192.168.50.202
```

## Commands

the commands have been deployed into `/usr/local/bin`

## Network ports

```bash
default-network-port = 9239
default-rpc-port = 9238
```

## Confirm it is working

1. on both hosts issue

   ``` BASH
   multichain-cli chain1 getinfo
   ```

   should return similair to

   ``` BASH
   {"method":"getinfo","params":[],"id":1,"chain_name":"chain1"}

   {
       "version" : "1.0 beta 1",
       "nodeversion" : 10000201,
       "protocolversion" : 10008,
       "chainname" : "chain1",
       "description" : "MultiChain chain1",
       "protocol" : "multichain",
       "port" : 9239,
       "setupblocks" : 60,
       "nodeaddress" : "chain1@192.168.50.202:9239",
       "burnaddress" : "1XXXXXXX53XXXXXXQXXXXXXXZMXXXXXXbUvreE",
       "incomingpaused" : false,
       "miningpaused" : false,
       "walletversion" : 60000,
       "balance" : 0.00000000,
       "walletdbversion" : 2,
       "reindex" : false,
       "blocks" : 59,
       "timeoffset" : 0,
       "connections" : 1,
       "proxy" : "",
       "difficulty" : 0.00000006,
       "testnet" : false,
       "keypoololdest" : 1491688543,
       "keypoolsize" : 2,
       "paytxfee" : 0.00000000,
       "relayfee" : 0.00000000,
       "errors" : ""
   }
   ```

1. On the first server issue

   ```bash 
   WALLET1_ADDRESS=`multichain-cli chain1 listpermissions issue | jq -r .[0].address`
   multichain-cli chain1 issue "$WALLET1_ADDRESS" asset1 1000 0.01
   ```

1. On both instance issue

   ``` bash
   multichain-cli chain1 listassets | jq .
   ```

   should return similair to

   ```bash
   {"method":"listassets","params":[],"id":1,"chain_name":"chain1"}

    [
        {
            "name": "asset1",
            "issuetxid": "b481b4f88a3ab4a3a01d5c237557f0e25b9c0e17b49f685a99babfda88783ab7",
            "assetref": "60-266-33204",
            "multiple": 100,
            "units": 0.01,
            "open": false,
            "details": {},
            "issueqty": 1000,
            "issueraw": 100000,
            "subscribed": false
        }
    ]
   ```

1. On server 1 issue to get the current balance of the wallet

   ``` bash
   multichain-cli chain1 gettotalbalances | jq .
   ```

   should return similair to

   ```bash
    {"method":"gettotalbalances","params":[],"id":1,"chain_name":"chain1"}

    [
        {
            "name": "asset1",
            "assetref": "60-266-33204",
            "qty": 1000
        }
    ]

   ```
1. On server 2 issue to get the current balance of the wallet

   ``` bash
   multichain-cli chain1 gettotalbalances | jq .
   ```

   should return similair to

   ```bash
    {"method":"gettotalbalances","params":[],"id":1,"chain_name":"chain1"}

    []
   ```
1. On Server 2 get the current wallet address

   ``` bash
   WALLET2_ADDRESS=`ssh 192.168.50.202 "multichain-cli chain1 getaddresses| jq -r .[0]"`
   ```

   On server 1 issue to send 100 units to the second servers wallet

   ``` bash
   multichain-cli chain1 send "$WALLET2_ADDRESS" asset1 100
   ```

   should return similair to

   ```bash
   {"method":"sendasset","params":["1KLNS3sXe1zzkeifecP9Y8crMELVvxDwhndNok","asset1",100],"id":1,"chain_name":"chain1"}

   c916fac7dd277459284e12d7b11ac93ba30fed06cd679bc228aa7418fbed9501
   ```

1. on both server 1 issue to get the current wallet balance

   ``` bash
   multichain-cli chain1 gettotalbalances | jq .
   ```

   it shuld return

   ``` bash
    {"method":"gettotalbalances","params":[],"id":1,"chain_name":"chain1"}

    [
        {
            "name": "asset1",
            "assetref": "60-266-33204",
            "qty": 900
        }
    ]

   ```

1. on both server 2 issue to get the current wallet balance

   ``` bash
   multichain-cli chain1 gettotalbalances | jq .
   ```

   it should return

   ``` bash
    {"method":"gettotalbalances","params":[],"id":1,"chain_name":"chain1"}

    [
        {
            "name": "asset1",
            "assetref": "60-266-33204",
            "qty": 100
        }
    ]

   ```

1. On server 1 issue to get a list of transactions

   ``` bash
   multichain-cli chain1 listwallettransactions  | jq .
   ```
  it should return

   ``` bash
    [
    {
        "balance": {
            "amount": 0,
            "assets": []
        },
        "myaddresses": [
            "147zE76VNAKMACgVv5iUi8YvxE8EnhpKaLUr82"
        ],
        "addresses": [],
        "permissions": [],
        "items": [],
        "data": [
            "53504b62473045022100899e617e7a313ac1225bbc73e009be6b5d72b419cb946336ac3ca71d8e404cf702201f92a71658bc289a65b79334092ee084d0c3fba9012f77236b4b14d9e0248a0c022102f276e3fdbb021ad3f95c364af729d509d918d0c8ca3309500b17180e0632473b"
        ],
        "confirmations": 92,
        "generated": true,
        "blockhash": "00de579abc6f189d10183e289b8cb80200b5821c429f8ae8fb51f80e5dc122fd",
        "blockindex": 0,
        "blocktime": 1491688534,
        "txid": "24a11d2a3d33c06eed1eb1f36537fdd4642a1d8407836460fdc3be83c065d377",
        "valid": true,
        "time": 1491688534,
        "timereceived": 1491688534
    }

    ]
   ```

1. On server 2 issue to get a list of transactions

   ``` bash
   multichain-cli chain1 listwallettransactions  | jq .
   ```
  it should return

   ``` bash
    [
        {
        "balance": {
            "amount": 0,
            "assets": []
        },
        "myaddresses": [
            "1KLNS3sXe1zzkeifecP9Y8crMELVvxDwhndNok"
        ],
        "addresses": [
            "147zE76VNAKMACgVv5iUi8YvxE8EnhpKaLUr82"
        ],
        "permissions": [
            {
            "for": null,
            "connect": true,
            "send": true,
            "receive": true,
            "create": false,
            "issue": false,
            "mine": false,
            "admin": false,
            "activate": false,
            "startblock": 0,
            "endblock": 4294967295,
            "timestamp": 1491688549,
            "addresses": [
                "1KLNS3sXe1zzkeifecP9Y8crMELVvxDwhndNok"
            ]
            }
        ],
        "items": [],
        "data": [],
        "confirmations": 89,
        "blockhash": "00dd2d8074953d1e6766904f90b61cad167c678930eed72b27049676a2616c2a",
        "blockindex": 1,
        "blocktime": 1491688564,
        "txid": "d6bbde558b783a5742597072b81a35c3862327456e1ef8c8de6811fe20556175",
        "valid": true,
        "time": 1491688563,
        "timereceived": 1491688563
        }
    ]
   ```

1. Follow the rest of the tutorial @ [http://www.multichain.com/getting-started/](http://www.multichain.com/getting-started/) from `5.Transaction Metadata`