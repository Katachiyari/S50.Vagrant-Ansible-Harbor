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

