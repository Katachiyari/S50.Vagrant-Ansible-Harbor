# Harbor TP - Étape 2 (Ansible Inventory + Test)

## Objectif

Configurer inventory Ansible et tester connexion VM.

## Fichiers

```
inventory/hosts
```


## Contenu `inventory/hosts`

```ini
[harbor]
harbor-vm ansible_host=192.168.56.20 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```


## Commandes Exécution

```bash
# 1. Créer inventory
mkdir -p inventory
cat > inventory/hosts << 'EOF'
[harbor]
harbor-vm ansible_host=192.168.56.20 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key ansible_ssh_common_args='-o StrictHostKeyChecking=no'
EOF

# 2. Permissions clé SSH
chmod 600 .vagrant/machines/default/virtualbox/private_key

# 3. Test connexion
ansible -i inventory/hosts harbor-vm -m ping
```

**Résultat attendu** :

```
harbor-vm | SUCCESS => {"changed": false, "ping": "pong"}
```


## Debug - Problèmes SSH Courants

### ❌ Erreur 1 : `Host key verification failed`

```
[ERROR]: Failed to connect to the host via ssh: Host key verification failed
```

**Solution** : Ajoute `ansible_ssh_common_args='-o StrictHostKeyChecking=no'`

### ❌ Erreur 2 : `Permission denied (publickey)`

```
vagrant@192.168.56.20: Permission denied (publickey)
```

**Solutions** :

```bash
# Fix 1 : Permissions clé
chmod 600 .vagrant/machines/default/virtualbox/private_key

# Fix 2 : Clé Vagrant dans inventory (obligatoire)
ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key
```


### ❌ Erreur 3 : Port SSH incorrect

```
ssh: connect to host 192.168.56.20 port 22: Connection refused
```

**Vérif** : `vagrant port` → SSH sur port `2222` (host)

### Test SSH Manuel

```bash
ssh -i .vagrant/machines/default/virtualbox/private_key -o StrictHostKeyChecking=no vagrant@192.168.56.20
```


## Vérifications Post-Installation

```bash
# Python OK pour Ansible
ansible -i inventory/hosts harbor-vm -m setup -a "filter=ansible_python*" | grep version

# UFW ports ouverts
ansible -i inventory/hosts harbor-vm -m shell -a "sudo ufw status"
```


## Statut Étape 2

```
✅ ansible ping = "pong" → Étape terminée
❌ Erreur SSH → Applique debug ci-dessus
```


## Structure Projet

```
harbor-tp/
├── Vagrantfile      ✅ Étape 1
├── inventory/hosts  ✅ Étape 2
├── README.md        ✅ Étape 1
└── (Étape 3...)
```

**Temps** : 2min
**Prérequis** : Étape 1 (VM vagrant up)[^1]

<div align="center">⁂</div>

[^1]: paste.txt

