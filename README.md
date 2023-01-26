# Fullnode-SUI
Instalation Fullnode SUI


<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/174559198-c1f612e5-bba2-4817-95a8-8a3c3659a2aa.png">
</p>

<a href="https://discord.gg/sui" target="_blank">Join our discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>

# Sui node setup for devnet

Official documentation:
- Official manual: https://docs.sui.io/build/fullnode
- Experiment with Sui DevNet: https://docs.sui.io/explore/devnet
- Check you node health: https://node.sui.zvalid.com/

## Minimum hardware requirements
- CPU: 2 CPU
- Memory: 4 GB RAM
- Disk: 50 GB SSD Storage

## Recommended hardware requirements
- CPU: 2 CPU
- Memory: 8 GB RAM
- Disk: 50 GB SSD Storage

> Storage requirements will vary based on various factors (age of the chain, transaction rate, etc) although we don't anticipate running a fullnode on devnet will require more than 50 GBs today given it is reset upon each release roughly every two weeks.

# Install Sui node
To setup Sui node follow the steps below

## 1. Update packages
```
sudo apt update && sudo apt upgrade -y
```

## 2. Install dependencies
```
sudo wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/download/v4.23.1/yq_linux_amd64 && chmod +x /usr/local/bin/yq
sudo apt-get install jq -y
```

## 3. Download sui binaries
```
version=$(wget -qO- https://api.github.com/repos/SecorD0/Sui/releases/latest | jq -r ".tag_name")
wget -qO- "https://github.com/SecorD0/Sui/releases/download/${version}/sui-linux-amd64-${version}.tar.gz" | sudo tar -C /usr/local/bin/ -xzf -
```

## 4. Download and update configs
```
mkdir -p $HOME/.sui
wget -qO $HOME/.sui/fullnode.yaml https://github.com/MystenLabs/sui/raw/main/crates/sui-config/data/fullnode-template.yaml
wget -qO $HOME/.sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob
yq -i ".db-path = \"$HOME/.sui/db\"" $HOME/.sui/fullnode.yaml
yq -i '.metrics-address = "0.0.0.0:9184"' $HOME/.sui/fullnode.yaml
yq -i '.json-rpc-address = "0.0.0.0:9000"' $HOME/.sui/fullnode.yaml
yq -i ".genesis.genesis-file-location = \"$HOME/.sui/genesis.blob\"" $HOME/.sui/fullnode.yaml
```

## 5. Create sui service
```
sudo tee /etc/systemd/system/suid.service > /dev/null <<EOF
[Unit]
Description=Sui node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which sui-node) --config-path $HOME/.sui/fullnode.yaml
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 6. Start sui node
```
sudo systemctl daemon-reload
sudo systemctl enable suid
sudo systemctl restart suid
```
