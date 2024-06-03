## Installation sous RHEL 9
https://docs.opennebula.io/6.8/installation_and_configuration/frontend_installation/install.html

```bash
# On désactive SELinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled' /etc/selinux/config
# On active les repos OpenNebula
cat << "EOT" > /etc/yum.repos.d/opennebula.repo
[opennebula]
name=OpenNebula Community Edition
baseurl=https://downloads.opennebula.io/repo/6.8/AlmaLinux/$releasever/$basearch
enabled=1
gpgkey=https://downloads.opennebula.io/repo/repo2.key
gpgcheck=1
repo_gpgcheck=1
EOT
rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
yum makecache
# Note: Avec AlmaLinux 9 une dépendance sur libmysqlclient existe et ne peut pas être satisfaite, car il existe une dépendance a mariadb et que les deux paquets sont incompatibles. mariadb-connector-c ne permet malheureusement pas de satisfaire la dépendance.
yum -y install opennebula opennebula-sunstone opennebula-fireedge opennebula-gate opennebula-flow opennebula-provision
```

### Installation de docker

On utilise les repos de centos:

```
yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
usermod -a -G docker oneadmin
systemctl enable --now docker
```

## OpenNebula Edge Clusters provisioning

```
yum -y install python3-pip
pip3 install 'cryptography<3.4'
pip3 install 'ansible>=2.8.0,<2.10.0'
pip3 install 'Jinja2>=2.10.0'
curl 'https://releases.hashicorp.com/terraform/0.14.7/terraform_0.14.7_linux_amd64.zip' | zcat >/usr/bin/terraform
chmod 0755 /usr/bin/terraform
```

## Configuration d'OpenNebula

### OpenNebula Daemon¶

sudo -u oneadmin /bin/bash
echo 'oneadmin:oneadmin' > /var/lib/one/.one/one_auth

## FireEdge (interface admin)

/etc/one/sunstone-server.conf	
:public_fireedge_endpoint: http://172.16.1.2:2616

On en profite pour désactiver le pare-feu:
systemctl stop firewalld

OneGate (Optional)¶

/etc/one/onegate-server.conf:

```
:host: 0.0.0.0
```

/etc/one/oned.conf

ONEGATE_ENDPOINT="http://172.16.1.2:5030"


/etc/one/oneflow-server.conf 
:host: 0.0.0.0

## Premier démarrage d'OpenNebula

systemctl enable --now opennebula opennebula-sunstone opennebula-fireedge opennebula-gate opennebula-flow
En tant que oneadmin, on vérifie que le frontend fonctionne:
sudo -u oneadmin oneuser show


```bash
[root@localhost ~]# sudo -u oneadmin oneuser show
USER 0 INFORMATION
ID              : 0
NAME            : oneadmin
GROUP           : oneadmin
PASSWORD        : 494a715f7e9b4071aca61bac42ca858a309524e5864f0920030862a4ae7589be
AUTH_DRIVER     : core
ENABLED         : Yes

TOKENS

USER TEMPLATE
TOKEN_PASSWORD="833816c1ad77c2f8876dfc5b55ca6fb0d477c6c5b2aba98fe993f4ee977ffa9e"

VMS USAGE & QUOTAS

VMS USAGE & QUOTAS - RUNNING

DATASTORE USAGE & QUOTAS

NETWORK USAGE & QUOTAS

IMAGE USAGE & QUOTAS
```

On se connecte à l'interface admin Sunstone:
http://172.16.1.2:9869/

Et à la nouvelle interface FireEdge:
http://172.16.1.2:2616/fireedge/sunstone

## Installation du déploiment Open Cluster

https://docs.opennebula.io/6.8/open_cluster_deployment/kvm_node/index.html

yum -y install opennebula-node-kvm
systemctl restart libvirtd

### On vérifie que l'authentification par clée ssh en tant que oneadmin est bien configurée sur chaque node:

ssh-copy-id -i /var/lib/one/.ssh/id_rsa.pub localhost
ssh-copy-id -i /var/lib/one/.ssh/id_rsa.pub Equipe paire

## Containers

https://docs.opennebula.io/6.8/management_and_operations/vm_management/container_images_usage.html#using-container-images-with-qemu-kvm


## NFS

On centralise le stockage sur l’infrastructure de l’équipe MAE.
On se configure donc en tant que client avec le FSTAB
Et coté fstab:
```
172.16.4.1:/mnt/opennebula/system /var/lib/one/datastores/0 nfs defaults,soft,intr,rsize=32768,wsize=32768 0 0
172.16.4.1:/mnt/opennebula/images /var/lib/one/datastores/1 nfs defaults,soft,intr,rsize=32768,wsize=32768 0 0
172.16.4.1:/mnt/opennebula/files /var/lib/one/datastores/2 nfs defaults,soft,intr,rsize=32768,wsize=32768 0 0
```

## Workaround d'un bug dans la configuration par défaut: double slash

https://forum.opennebula.io/t/solved-error-monitoring-host-when-trying-to-add-host/6838

Dans ``/etc/one/oned.cfg``:
Remplacer ``/var/lib/one//datastores`` par ``/var/lib/one/datastores``

## Bug réseau

Créer un bridge enslavant l'interface physique rend inopérante la connection réseau de l'hote, même si l'interface physique garde son IP (ce qui n'est déjà pas censé être possible).

Aucun workaround trouvé, pour cette raison nous passerons sur Proxmox pour le reste de cette SAÉ.

## Bug de hôte en state "ERROR"

Pour une raison inconnue, l'hôte passe aléatoirement en state ERROR.


## Utilisation du CLI


## Oneimage 

oneimage list
oneimage delete --force # Pour supprimer dans une image dans OpenNebula dans le fichier sous-jacent a été supprimé
