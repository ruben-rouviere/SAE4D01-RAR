
## Installation de docker

https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script


## Installation de container lab

https://containerlab.dev/install/

## Ajout du repo

```bash
echo "deb [trusted=yes] https://apt.fury.io/netdevops/ /" | \
sudo tee -a /etc/apt/sources.list.d/netdevops.list
sudo apt update && sudo apt install containerlab
```