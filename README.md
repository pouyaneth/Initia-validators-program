#**Initia-validator-node**


##**System requirements:**




CPU: 4 cores

Memory: 16GB RAM

Disk: 1 TB SSD Storage

Bandwidth: 100 Mbps




##**Install prerequisities:**




```sudo apt update && sudo apt upgrade -y
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```




##**Install Go:**





```cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```




##**Initia:**




```git clone https://github.com/initia-labs/initia.git
cd initia
git checkout v0.2.15
git switch -c v0.2.15
make install
initiad version
```




##**Moniker:**



```echo 'export MONIKER="my_node"' >> ~/.bash_profile
echo 'export CHAIN_ID="initiation-1"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile  
source $HOME/.bash_profile
```

*change the moniker*

```cd $HOME
initiad init $MONIKER --chain-id $CHAIN_ID
initiad config set client chain-id $CHAIN_ID
initiad config set client node tcp://localhost:$RPC_PORT
initiad config set client keyring-backend os
```




#**Genesis:**



```wget https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/genesis.json -O $HOME/.initia/config/genesis.json```




##**Peers:**




```PEERS="0aa35c1eaa0933218089b5fb1876e2d46d8f2240@37.60.229.199:27656,ebefecfd04824cfbd674bd4c0fcfd9315aadc210@103.170.155.158:27656,f9c8aa40fc2dad2c379316e33e4e855a0b40b2c7@109.199.109.89:27656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:17956,7e07c1d69e3f726d985614728c601b759f008e52@213.199.57.83:27656,818637c0b4fa7d9ced2283a4853b3f33fc55bb2d@95.111.224.8:26656,57fd42ad4b6d70868e645f17d846d7a6833404a9@194.180.188.44:27656,fc37e22ae9405cf00a775a014366d428376e47b3@37.27.48.77:29656,4701e15068475260a267aabe9b4df8addbb1f992@144.91.79.216:26656,96a259e4cbcde828faed933179784614cd0ea8d1@185.130.226.218:27656,bb37fee58e592edbe8acb627be937d9d17f0ef59@129.146.80.192:27656,6c47bb7c20367964f79755be5bfd4a06c50bae5a@159.89.11.119:27656,7b8ef017be344d202f65628281f5a166dafb6ec0@213.199.34.19:27656,14a53cee4fded2635149228e92b7a752971c40fb@185.130.226.121:27656,616900b4fc44040e4539821fa986ddb85b6f7b87@77.237.238.175:27656,7a98741ef8f7f42113779ccd952635b5a43a7d4f@192.3.128.80:27656,4f9b24bbe1d8a108f4f856d4125a7a6457b1b8cd@142.132.199.231:27656,a1de81504903d857695804f34e5bc1c1b9fc734a@84.46.242.232:27656,9fb117a08ac24032bca52914bb089e0a8df5a239@185.130.227.28:27656,fc935f2f3e3cfebef7d73abc9958a46b308366f4@109.199.122.112:27656,fe19d84d88e615dc100bac74dd743400b4d082d1@195.26.249.58:27656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml
```



*look for the latest peers in Initia discord*






##**prunning, set gas, port and some other steps:**





```EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=26658 \
P2P_PORT=26656 \
PPROF_PORT=6060 \
API_PORT=1317 \
GRPC_PORT=9090 \
GRPC_WEB_PORT=9091
```






```sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.initia/config/config.toml
```





```sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" \
    $HOME/.initia/config/app.tom
```





```ed -i \
    -e "s/^pruning *=.*/pruning = \"custom\"/" \
    -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" \
    -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" \
    "$HOME/.initia/config/app.toml"
```






```sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.15uinit,0.01uusdc\"/" $HOME/.initia/config/app.toml```





```sed -i "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.initia/config/config.toml```





```sudo tee /etc/systemd/system/initiad.service > /dev/null <<EOF
[Unit]
Description=Initia Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which initiad) start --home $HOME/.initia
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```





```sudo systemctl daemon-reload && \
sudo systemctl enable initiad && \
sudo systemctl restart initiad && \
sudo journalctl -u initiad -f -o cat
```





##**Snapshot download:**




```sudo apt update

sudo apt install snapd -y

sudo snap install lz4

sudo systemctl stop initiad

wget -O initia_202803.tar.lz4 https://snapshots.polkachu.com/testnet-snapshots/initia/initia_202803.tar.lz4 --inet4-only

sudo systemctl restart initiad

cp ~/.initia/data/priv_validator_state.json  ~/.initia/priv_validator_state.json

initiad tendermint unsafe-reset-all --home $HOME/.initia --keep-addr-book

mv $HOME/.initia/priv_validator_state.json.backup $HOME/.initia/data/priv_validator_state.json

lz4 -c -d initia_202803.tar.lz4  | tar -x -C $HOME/.initia

cp ~/.initia/priv_validator_state.json  ~/.initia/data/priv_validator_state.json
```



*download the latset snapshot from the Initia discord*







```sudo systemctl restart initiad && sudo journalctl -u initiad -f -o cat```



```initiad status | jq```



*check the status, if you got False everything is fine, other than that try to change peers and snapshot.*








##**Wallet:**




`New wallet`





```initiad keys add $WALLET_NAME```


*dont remember to save your recovery phrase*







`Recover a wallet`




```initiad keys add --recover $WALLET_NAME```






##**Faucet:**





https://faucet.testnet.initia.xyz/  
https://discord.gg/initia






##**checking balance:**





```initiad q bank balances $(initiad keys show $WALLET_NAME -a)```






##**Creating validator:**






```initiad tx mstaking create-validator \
  --amount=5000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=Crypton \
  --chain-id=$CHAIN_ID \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --from=wallet \
  --identity="" \
  --website="https://github.com/cryptoneth/" \
  --details="every thing will go down but there is no gravity here" \
  --node=http://localhost:26657 \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.15uinit \
  -y
  ```








##**Delegate:**







```initiad tx mstaking delegate $(initiad keys show wallet --bech val -a)  5000000uinit --from wallet --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:26657 -y```







##**Vote:**






```initiad tx gov vote 20 yes --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:26657 -y```




*change the 20 with active proposals*





##**Oracle:**



```cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v0.4.3
git switch -c v0.4.3
```



```make build```



```mv build/slinky /usr/local/bin/```




```sudo tee /etc/systemd/system/slinkyd.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 127.0.0.1:15090
Restart=on-failure
RestartSec=30
LimitNOFILE=65535
  
[Install]
WantedBy=multi-user.target
EOF
```





```sudo systemctl daemon-reload
sudo systemctl enable slinkyd.service
sudo systemctl restart slinkyd.service

journalctl -fu slinkyd --no-hostname
```






```nano /root/.initia/config/app.toml```


*go to oracle section and change the parameteres with these:*



enabled = "true"
oracle_address = "127.0.0.1:8080"
client_timeout = "500ms"

*ctrl + x* then *y* and *enter*




*Exrecute this*


```cd
sudo systemctl daemon-reload
sudo systemctl restart initiad
sudo systemctl restart slinkyd.service
sudo journalctl -u initiad.service -f --no-hostname -o cat
```





*Check the validator status*





```initiad q mstaking validator $(initiad keys show $WALLET_NAME --bech val -a)```




##*Well done*



*Fill the form*



https://docs.google.com/forms/d/e/1FAIpQLSc09Kl6mXyZHOL12n_6IUA8MCcL6OqzTqsoZn9N8gpptoeU_Q/viewform?usp=send_form






















