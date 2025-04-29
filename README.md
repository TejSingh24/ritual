# ritual
* Start Docker before doing below steps

### Install Dependecies
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt -qy install curl git jq lz4 build-essential screen
```

### Docker Installation
```
sudo apt install docker.io
```
* If this shows error like following packages have unmet dependencies:- run the below commands
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
```
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
```
sudo docker run hello-world
```
* See if showed any error, there shouldn't be any, if did try running above commands from top again.

### Install Docker Compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```
sudo chmod +x /usr/local/bin/docker-compose
```
```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
```
```
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```
```
docker compose version
```
### User to docker group
```
sudo usermod -aG docker $USER
```
```
sudo reboot
```
```
docker run hello-world
```
### Starter Repository

```
git clone https://github.com/ritual-net/infernet-container-starter
```
```
cd infernet-container-starter
```
```
screen -S ritual
```
```
project=hello-world make deploy-container
```
* This should show you endless codes, may contain errors, ignore them
* Exit screen with `CTRL + A + D`

### Node Configuration
```
nano ~/infernet-container-starter/deploy/config.json
```
Make these below changes

1. RPC URL:- `RPC URL: https://mainnet.base.org/`
2. Private Key: your private key with 0x. have to write this 0x as well
3. Registry_address/Registry: `0x3B1554f346DFe5c482Bb4BA31b880c1C18412170`
4.     "snapshot_sync": {
        "sleep": 3,
        "starting_sub_id": 160000,
        "batch_size": 800,
        "sync_period": 30
    }
5. trail_head_blocks: `3`

* Save and exit with `CTRL + X` then `Y` then press `enter`

```
nano ~/infernet-container-starter/projects/hello-world/container/config.json
```
Make these below changes

1. RPC URL:- `RPC URL: https://mainnet.base.org/`
2. Private Key: your private key with 0x. have to write this 0x as well
3. Registry_address/Registry: `0x3B1554f346DFe5c482Bb4BA31b880c1C18412170`

* Save and exit with `CTRL + X` then `Y` then press `enter`

```
nano ~/infernet-container-starter/projects/hello-world/contracts/script/Deploy.s.sol
```
* make these below change
  1.  Registry_address/Registry: `0x3B1554f346DFe5c482Bb4BA31b880c1C18412170`

* Save and exit with `CTRL + X` then `Y` then press `enter`

Changes in make file
```
nano ~/infernet-container-starter/projects/hello-world/contracts/Makefile
```
Make these changes:-
1. Sender's address:- your private key
2. RPC_URL: `https://mainnet.base.org/`

* Save and exit with `CTRL + X` then `Y` then press `enter`

* Update Node Version
```
nano ~/infernet-container-starter/deploy/docker-compose.yaml
```
Change node version at top from 1.2.0 to 1.4.0
* For auto-restart, can add/change `restart:on-failure`
* Save and exit with `CTRL + X` then `Y` then press `enter`

### Restart Docker
```
docker compose -f infernet-container-starter/deploy/docker-compose.yaml down
```
```
docker compose -f infernet-container-starter/deploy/docker-compose.yaml up
```

* open new WSL ubantu window and check dockers, if started or not
  ```
  docker ps
  ```

### Install Foundry
```
cd
```
```
mkdir foundry
```
```
cd foundry
```
```
curl -L https://foundry.paradigm.xyz | bash
```
```
source ~/.bashrc
```
```
foundryup
```
* If this shows error like git submodule exited with code 120 or something, this means it already exists but then need to remove that and add again follow the below commands:
  ```
  rm -rf projects/hello-world/contracts/lib/forge-std
  ```
  ```
  forge install --no-commit foundry-rs/forge-std
  ```
  ```
  cd ~/infernet-container-starter/projects/hello-world/contracts
  ```
  ```
  rm -rf lib/forge-std
  ```
  ```
  forge install --no-commit foundry-rs/forge-std
  ```
  ```
  ls lib/forge-std
  ```
  ```
  foundryup
  ```

### Installing necessary dependencies
```
cd ~/infernet-container-starter/projects/hello-world/contracts
```
```
forge install --no-commit foundry-rs/forge-std
```
* this also show error if already there, need to remove it and add again by this:
  ```
  sudo rm /usr/bin/forge
  ```
  ```
  export PATH="/root/.foundry/bin:$PATH"
  ```
  ```
  forge install --no-commit foundry-rs/forge-std
  ```
```
forge install --no-commit ritual-net/infernet-sdk
```
* If this also showed error, remove and add:
  ```
  cd ~/infernet-container-starter/projects/hello-world/contracts
  ```
  ```
  rm -rf lib/infernet-sdk
  ```
  ```
  forge install --no-commit ritual-net/infernet-sdk
  ```
  ```
  ls lib/infernet-sdk
  ```

```
cd ../../../
```

# Deploying Consumer Contract
```
docker compose -f infernet-container-starter/deploy/docker-compose.yaml down
```
```
docker compose -f infernet-container-starter/deploy/docker-compose.yaml up
```

Open another wsl window
```
docker logs infernet-node
```
* Check logs for any error, if not continue in new wsl window again

```
cd ~/infernet-container-starter
```
```
project=hello-world make deploy-contracts
```


![image](https://github.com/user-attachments/assets/a11b444a-4354-4a70-9b6e-3fe6afe4f237)

If everything was successful, you will see a new transaction.


### Call Contract
Look at the logs and check to which address your SaysGM contract was deployed to.


![image](https://github.com/user-attachments/assets/b4e2d81b-76d4-4330-8f2c-ad51e186b2e3)

Copy the contract address to paste it in preconfigured address

```
nano ~/infernet-container-starter/projects/hello-world/contracts/script/CallContract.s.sol
```
Change in this place, replace the existing to your contract address:
SaysGM saysGm = SaysGM(0x13D69Cf7d6CE4218F646B759Dcf334D82c023d8e)

* Save and exit with `CTRL + X` then `Y` then press `enter`

```
cd ~/infernet-container-starter
```
```
project=hello-world make call-contract
```

Please wait for a few moments. You will then see a new contact from the Infernet Node.
![image](https://github.com/user-attachments/assets/fb525abb-03ec-4260-b819-0bb63de4c40f)

If this type of error shows up:
Error: Could not instantiate forked environment 
or
Error: Could not instantiate forked environment

then  switch to a more powerful RPC as the free RPC on base.org may not be able to handle the load.

### verify node setup
```
docker logs infernet-node
```







  














  
