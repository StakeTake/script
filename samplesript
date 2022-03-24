#!/bin/bash

while true
do

# Logo

echo "============================================================"
curl -s https://raw.githubusercontent.com/AlekseyMoskalev1/script/main/Noders.sh | bash
echo "============================================================"

# Menu

PS3='Select an action: '
options=(
"Install Docker and update ATP"
"Run Aptos Node"
"Status Node"
"Log Node"
"Exit")
select opt in "${options[@]}"
do
case $opt in

# Docker

"Install Docker and update ATP")
echo "============================================================"
echo "Docker installation started"
echo "============================================================"

sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release wget jq sed -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
docker version

mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
sudo chown $USER /var/run/docker.sock
docker compose version

echo "============================================================"
echo "Docker installed"
echo "============================================================"
break
;;

"Run Aptos Node")
echo "============================================================"
echo "Installing"
echo "============================================================"
mkdir $HOME/aptos
cd $HOME/aptos
wget https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/public_full_node/docker-compose.yaml
wget https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/public_full_node/public_full_node.yaml
wget https://devnet.aptoslabs.com/genesis.blob
wget https://devnet.aptoslabs.com/waypoint.txt
mkdir $HOME/aptos/identity
docker run --rm --name aptos_tools -d -i aptoslab/tools:devnet
docker exec -it aptos_tools aptos-operational-tool generate-key --encoding hex --key-type x25519 --key-file $HOME/private-key.txt
docker exec -it aptos_tools cat $HOME/private-key.txt > $HOME/aptos/identity/private-key.txt
docker exec -it aptos_tools aptos-operational-tool extract-peer-from-file --encoding hex --key-file $HOME/private-key.txt --output-file $HOME/peer-info.yaml > $HOME/aptos/identity/id.json
PEER_ID=$(cat $HOME/aptos/identity/id.json | jq -r '.Result | keys[]')
PUBLIC_KEY=$(cat $HOME/aptos/identity/id.json | jq -r '.. | .keys?  | select(.)[]')
PRIVATE_KEY=$(cat $HOME/aptos/identity/private-key.txt)
docker stop aptos_tools
cd $HOME/aptos
sed -i '/      discovery_method: "onchain"$/a\
      identity:\
          type: "from_config"\
          key: "'$PRIVATE_KEY'"\
          peer_id: "'$PEER_ID'"' public_full_node.yaml
          
docker compose up -d

break
;;

"Status Node")

curl 127.0.0.1:9101/metrics 2> /dev/null | grep aptos_state_sync_version | grep type
break
;;

"Log Node")
docker logs -f aptos-fullnode-1 --tail 500
break
;;

"Exit")
exit
;;
*) echo "invalid option $REPLY";;
esac
done
done
