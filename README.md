# Cluster Kubernetes local com KIND

# NFS Provisioner

Para habilitar o nfs, é preciso primeiro configurar um servidor nfs funcional.

Vamos instalar o pacote `nfs-server` para configurar o host:

```bash
sudo apt-get update && sudo apt-get upgrade

sudo apt-get install nfs-server
```

Vamos criar o diretório que será compartilhado e ativar o serviço:

```bash
sudo mkdir -p /srv/nfs/my-cluster

# Compartilhe o diretório com cada nó do cluster!
sudo cat <<EOF >> /etc/exports
/srv/nfs/my-cluster     <node_ip>(rw,sync,no_subtree_check,no_root_squash)
EOF

sudo systemctl enable --now nfs-server
sudo exportfs -ar
```

Agora instalamos o provedor nfs utilizando helm.

```bash
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner -f ./nfs-provisioner-values.yml
```