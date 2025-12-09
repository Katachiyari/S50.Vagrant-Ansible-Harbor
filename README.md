# 🚀 **Harbor TP DevOps - Documentation Complète**

## 🎯 **Vue d'ensemble du TP**

Déploiement **100% automatisé** d'**Harbor 2.11.1** sur VM **Debian Vagrant** via **Ansible**. Environnement d'apprentissage (1Go RAM, 2 CPU).

**Durée totale** : **25 minutes**

## 📋 **Structure Projet Finale**

```
harbor-tp/
├── Vagrantfile                    # ✅ Étape 1
├── inventory/hosts                # ✅ Étape 2
├── group_vars/all.yml             # ✅ Étape 4
├── setup_harbor.yml               # ✅ Étape 6
├── roles/01-docker/               # ✅ Docker 29.1.2
├── roles/02-harbor-download/      # ✅ Harbor 2.11.1
├── roles/03-certs/                # ✅ SSL harbor.local
└── roles/04-harbor-install/       # ✅ Installation
```


## 🚀 **Étapes Détaillées**

### **Étape 1 : VM Vagrant 🖥️** `vagrant up`

```
VM Debian Bookworm 1Go RAM / 2 CPU / 40Go disque
IP fixe : 192.168.56.20
UFW : Ports Harbor ouverts
```


### **Étape 2 : Ansible 🔌** `ansible -m ping`

```ini
[harbor]
harbor-vm ansible_host=192.168.56.20 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key
```


### **Étape 3 : Docker 🐳** `roles/01-docker`

```
Docker CE 29.1.2 + Compose v5.0.0
vagrant ∈ groupe docker (no sudo)
```


### **Étape 4 : Harbor Download 📦** `roles/02-harbor-download`

```
harbor-offline-installer-v2.11.1.tgz (662Mo)
→ /home/vagrant/harbor/harbor/
```


### **Étape 5 : Certificats SSL 🔒** `roles/03-certs`

```
sscg --hostname harbor.local → 3 certificats
/etc/ssl/certs/harbor.local.crt
/etc/docker/certs.d/harbor.local:443/ca.crt
```


### **Étape 6 : Installation 🎉** `setup_harbor.yml`

```
cp harbor.yml.tmpl harbor.yml + sed hostname/certificats
sudo ./install.sh → 16 conteneurs UP
docker compose up -d
```


## 🌐 **PORTS VAGRANT - ACCÈS CORRECT** ✅

| Service | VM Guest | Host Navigateur | Protocole | Statut |
| :-- | :-- | :-- | :-- | :-- |
| **UI Harbor** | **443** | **https://localhost:8443** | **HTTPS** | ✅ **PRINCIPAL** |
| **HTTP Redirect** | **80** | http://localhost:2200 | **→ HTTPS** | 308 Redirect |
| **Docker Registry** | **443** | `localhost:8443` | HTTPS | ✅ |
| **SSH** | **22** | `vagrant ssh` | SSH | ✅ |

**Vérification** : `vagrant port`

```
80 (guest) => 2200 (host)     # HTTP → HTTPS redirect
443 (guest) => 8443 (host)    # HTTPS HARBOR UI ✅
```


## 🎮 **ACCÈS IMMÉDIAT**

```
🌐 https://localhost:8443              ← UI Harbor
👤 admin / Harbor12345                 ← Login
⚠️ Accepte alerte SSL (cert auto-signé)

🐳 Docker :
docker login localhost:8443
admin / Harbor12345
docker push localhost:8443/test:latest
```


## 🛠️ **Commandes Production**

```bash
# Status
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose ps" -b

# Arrêt/Redémarrage
cd /home/vagrant/harbor/harbor && docker compose down/up -d

# Logs
docker compose logs -f nginx
```


## 🔍 **Vérifications**

```bash
# API
curl -k https://localhost:8443/api/systeminfo

# Services (healthy)
docker compose ps

# Ressources
docker stats
```


## 📊 **Architecture**

```
16 conteneurs UP (healthy) :
├── nginx (proxy 443→8443)
├── harbor-core (API)
├── harbor-portal (UI)
├── registry (Docker)
├── postgresql, redis
└── trivy-adapter (vuln scan)
RAM : 800Mo | Disque : 2Go
```


## 🎓 **Concepts Maîtrisés**

- **Vagrant** : VM + ports forwarding
- **Ansible** : Rôles + inventory SSH
- **Docker** : Compose multi-services
- **Harbor** : Registry entreprise + SSL


## 🐛 **Debug Résolus**

| Erreur | Fix |
| :-- | :-- |
| SSH Vagrant | `chmod 600 private_key` |
| harbor.yml | `cp harbor.yml.tmpl + sed` |
| SSL sscg | `--hostname` |
| Ports | `https://localhost:8443` |

## 📈 **Ressources**

```
VM : 1Go RAM / 2 CPU / 40Go
Harbor : 800Mo RAM / 2Go disque
Temps : 25min
```


## 🎖️ **TP TERMINÉ ✅**

**https://localhost:8443** → **admin/Harbor12345**

**Auteur** : Guide Vagrant/Ansible/Harbor 2.11.1
**Date** : 09 Décembre 2025[^1][^2]

<div align="center">⁂</div>

[^1]: https://github.com/goharbor/harbor/issues/10958

[^2]: https://github.com/goharbor/harbor/blob/main/make/harbor.yml.tmpl

