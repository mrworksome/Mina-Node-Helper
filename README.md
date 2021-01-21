# Mina-Node-Helper

## How run Node?
Ubuntu 18.04 / Debian 9

### Update sistem and install node
```
sudo apt-update
sudo apt-get install -y apt-transport-https ca-certificates
```

1.1. First remove any previously installed version of the daemon to prevent any errors when upgrading to the latest version. 

```
sudo apt-get remove -y mina-testnet-postake-medium-curves
echo "deb [trusted=yes] http://packages.o1test.net release main" | sudo tee /etc/apt/sources.list.d/mina.list
sudo apt-get update
sudo apt-get install -y curl unzip mina-testnet-postake-medium-curves=0.2.6-5c08d6d --allow-downgrades
```
- check version coda with command`coda version`
- return `Commit [DIRTY]5c08d6d51111d31269e77f3c62a544b3262ee4c8 on branch HEAD`

### Setup Keypair

1.2. - For Testworld, we are sending out pre-generated keypairs to every participant. Once you receive the link to claim your keypair, download it onto your computer.
```
mkdir ~/keys
wget -O ~/keys/new-keys.zip <keypair-download-url>
```
1.3 - Unzip recieved keys
```
apt install unzip
cd keys
unzip new-keys.zip
```
Example return
```
Archive: new-keys.zip
  inflating: extra_fish_account_9999
 extracting: extra_fish_account_9999.pub
 ```
1.4. - Rename them for our convenience:
```
mv extra_fish_account_9999 my-wallet
mv extra_fish_account_9999.pub my-wallet.pub
```
exit from directories
`cd ` or `cd .`

1.5. - Set access rights for our keys:
```
echo 'export KEYPATH=$HOME/keys/mina-wallet' >> $HOME/.bashrc
echo 'export MINA_PUBLIC_KEY=$(cat $HOME/keys/mina-wallet.pub)' >> $HOME/.bashrc
source ~/.bashrc
```
## Peer list

2.1. Download the list of peers 
```
wget -O ~/peers.txt https://raw.githubusercontent.com/MinaProtocol/coda-automation/bug-bounty-net/terraform/testnets/testworld/peers.txt
```

2.2.
TODO: add script for update peers

## Start up a node
3.0
if you run node in Hetzner need this additional commands
```
sudo apt install ufw
ufw allow 22
ufw allow 80
ufw allow 443
ufw enable
```

3.1. - Run Daemon
```
CODA_PRIVKEY_PASS="password from email" coda daemon -peer-list-file ~/peers.txt -block-producer-key ~/keys/my-wallet -generate-genesis-proof true -file-log-level Info -super-catchup
```
Existing coda daemon process with `Ctrl-C`
3.2. - Create `~/.mina-env`
```
touch ~/.mina-env
```
Open this file
```
nano ~/.mina-env
```
Copy this and paste
```
CODA_PRIVKEY_PASS="password from email"
EXTRA_FLAGS=" -file-log-level Debug -super-catchup"
```
exit from file and save 
```
ctrl+X
y
Enter
```
3.3 Run the following commands to start up a Mina node instance and connect to the live network:
```
systemctl --user daemon-reload
systemctl --user start mina
systemctl --user enable mina
sudo loginctl enable-linger
```
Mina process running in the background and auto-restarting.
Command for working with mina
```
systemctl --user status mina  # check status mina
systemctl --user stop mina  # stop and restarting mina
systemctl --user restart mina  # restart mina
```
## See Logs
```
journalctl --user -u mina -n 1000 -f
journalctl --user -u mina -n 1000 -f | grep "Info" | grep "Snark Worker" # check snark worker work
```

## Manage Account
4.1 Import account
```
coda accounts import -privkey-path ~/keys/my-wallet
```
4.2. Unlock account
```
coda accounts unlock -public-key $MINA_PUBLIC_KEY
```
Paste code from email and create new pass
4.3. Show balance
```
coda accounts list
```
4.3 First transaction

After the node has received the Synced status, you can try to send your first transaction
```
coda client send-payment \
  -amount 0.5 \
  -receiver B62qndJi5mnRoBZ8SAYDM1oR2SgAk5WpZC8hGpJUZ4e64kDHGbFMeLJ \
  -fee 0.1 \
  -sender $MINA_PUBLIC_KEY
```





