# Harbor TP - VM Vagrant (Étape 1)

## Vue d'ensemble

VM Debian Bookworm64 (1Go RAM, 2 CPU) prête pour installation Harbor via Ansible.

## Prérequis

- **Vagrant** 2.4+
- **VirtualBox** 7.0+
- Répertoire projet : `harbor-tp/`


## Structure Fichiers

```
harbor-tp/
├── Vagrantfile      # Configuration VM
└── README.md        # Ce fichier
```
## ./vagrantfile
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.box_version = ">= 12.20250126.1"  # Version qui existe
  
  config.vm.network "private_network", ip: "192.168.56.20"
  config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
  config.vm.network "forwarded_port", guest: 443, host: 8443, auto_correct: true
  config.vm.network "forwarded_port", guest: 4443, host: 4443, auto_correct: true
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 2
    vb.name = "harbor-tp"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive
    apt-get update && apt-get upgrade -y
    apt-get install -y curl wget sudo ufw python3-pip
    
    useradd -m -s /bin/bash vagrant || true
    echo "vagrant:vagrant" | chpasswd
    usermod -aG sudo vagrant
    echo "vagrant ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/vagrant
    
    ufw --force reset
    ufw default deny incoming
    ufw default allow outgoing
    ufw allow 22/tcp && ufw allow 80/tcp && ufw allow 443/tcp && ufw allow 4443/tcp
    ufw --force enable
    
    echo "VM Harbor TP prête - IP: 192.168.56.20"
  SHELL
end

```

## Lancement VM

```bash
vagrant up
```

**Ports auto-corrigés** (Vagrant affiche les ports finaux) :

```
80   → 2200 (Harbor HTTP)
443  → 8443 (Harbor HTTPS)  
4443 → 4443 (Harbor DCT)
22   → 2222 (SSH)
```


## Accès

```bash
# SSH
vagrant ssh

# IP fixe (Ansible)
192.168.56.20
```


## Ce que fait le Provisionning

1. **Upgrade système** : `apt-get upgrade -y`
2. **Paquets** : `curl wget sudo ufw python3-pip`
3. **User vagrant** : sudo sans mot de passe
4. **Firewall UFW** : Ports 22/80/443/4443 ouverts

## Vérifications

```bash
# Dans la VM
ufw status              # Firewall OK
python3 --version       # Ansible prêt
ip addr show            # IP 192.168.56.20
```


## Nettoyage

```bash
vagrant destroy -f      # Supprime VM
vagrant box prune       # Nettoie boxes inutilisées
```


## Inventory Ansible (Prochaine Étape)

```ini
[harbor]
harbor-vm ansible_host=192.168.56.20 ansible_user=vagrant
```

**Test** : `ansible -i inventory/hosts harbor-vm -m ping`

## Statut

✅ **VM prête pour Ansible Harbor**
**Temps déploiement** : ~3min
**Disque** : 20Go (extensible)

