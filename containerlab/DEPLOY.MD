## Déploiement

```bash
container deploy
```

## Connexion en CLI à CEOS

```bash
docker exec -it clab-rar-ceos Cli
```

## Pour info: Informations MAE

AS42
IP Routeur (interface salle): 10.202.166.10

## Configuration CEOS

````cisco
enable
conf t
ip routing
interface Ethernet 2
	desc ITCO Salle
	no switchport
	ip address 10.202.166.11/16
	exit
router bgp 654242
	neighbor 10.202.166.10 remote-as 42
	exit
wr mem
```