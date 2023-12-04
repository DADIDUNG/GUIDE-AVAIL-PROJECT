# GUIDE-AVAIL-PROJECT
**#GUIDE SYSTEMD:**
#UPDATE HE THONG:
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y

#TAO THU MUC:

mkdir -p $HOME/.avail && cd $HOME/.avail

#TAI AVAIL VE THU MUC:

wget https://github.com/availproject/avail/releases/download/v1.8.0.3/x86_64-ubuntu-2204-data-avail.tar.gz
tar -xvf x86_64-ubuntu-2204-data-avail.tar.gz
mv data-avail /usr/bin/avail

#CHECK VERSION

avail --version

#TAI JSON:

wget -O $HOME/.avail/chainspec.raw.json "https://goldberg.avail.tools/chainspec.raw.json"
chmod 744 ~/.avail/chainspec.raw.json
sha256sum $HOME/.avail/chainspec.raw.json

#TAO SYSTEMD

yourname=DADID_NHT
tee /etc/systemd/system/avail.service > /dev/null << EOF
[Unit]
Description=Avail Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/bin/avail \
  --base-path $HOME/.avail/data/ \
  --chain $HOME/.avail/chainspec.raw.json \
  --port 30333 \
  --rpc-port 9933 \
  --prometheus-port 9615 \
  --validator \
  --name '$yourname'
[Install]
WantedBy=multi-user.target
EOF

#RUN SYSTEMD:
systemctl daemon-reload
systemctl enable avail
systemctl restart avail && journalctl -u avail -f -o cat

#STOP NODE AVAIL:
sudo systemctl stop avail
sudo systemctl disable avail
sudo rm /etc/systemd/system/avail.service
sudo systemctl daemon-reload
cd
rm -r .avail


#TAO HEX KEY FOR VALIDATOR: 
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9944

**#GUIDE DOCKER: **
#CHAY DOCKER
sudo docker pull availj/avail:v1.8.0.3

Hoặc: 
cd avail
sudo docker run -v $(pwd)/state:/da/state:rw -v $(pwd)/keystore:/da/keystore:rw --network host -d --restart unless-stopped availj/avail:v1.8.0.3 --chain goldberg --validator --name "DADID_VN" -d /da/state

#CHECK LOGS:
docker logs avail -f
