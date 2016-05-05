# vagrant-multichain

issue the `vagrant up` command and it should do all the heavy lifting.

If that fails try
`ansible-playbook -i static_inventory provision-multichain.yml  -u vagrant`

You should be able to follow from step 3 at (http://www.multichain.com/getting-started/)[http://www.multichain.com/getting-started/]

# Connecting 
In the project directory use `ssh` with any of the following 
```
ssh -i roles/multichain/files/id_rsa root@192.168.50.201
ssh -i roles/multichain/files/id_rsa root@192.168.50.202
```

# Commands
the commands have been deployed into `/usr/local/bin`

#Network ports
```
default-network-port = 9239
default-rpc-port = 9238
```
