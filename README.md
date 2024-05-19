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


`git clone https://github.com/initia-labs/initia.git
cd initia
git checkout v0.2.15
git switch -c v0.2.15
make install
initiad version`
