<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 🚀 **Harbor TP DevOps - Documentation Complète**

## 🎯 **Vue d'ensemble du TP**

Déploiement **100% automatisé** d'**Harbor 2.11.1** (registre Docker entreprise) sur VM **Debian Vagrant** via **Ansible**. Environnement d'apprentissage (1Go RAM, 2 CPU).

**Durée totale** : **25 minutes**
**Objectif pédagogique** : Maîtriser Vagrant + Ansible + Docker + Harbor

## 📋 **Structure Projet Finale**

```
harbor-tp/                          # Répertoire racine
├── Vagrantfile                    # ✅ Étape 1 : VM Debian
├── bootstrap.sh                   # Provisionning initial
├── inventory/
│   └── hosts                      # ✅ Étape 2 : Ansible inventory
├── group_vars/
│   └── all.yml                    # ✅ Étape 4 : Variables Harbor
├── setup_harbor.yml               # ✅ Étape 6 : Playbook MASTER
├── test-*.yml                     # Tests intermédiaires
├── roles/
│   ├── 01-docker/                 # ✅ Étape 3 : Docker 29.1.2
│   │   └── tasks/main.yml
│   ├── 02-harbor-download/        # ✅ Étape 4 : Harbor 2.11.1
│   │   └── tasks/main.yml
│   ├── 03-certs/                  # ✅ Étape 5 : SSL harbor.local
│   │   └── tasks/main.yml
│   └── 04-harbor-install/         # ✅ Étape 6 : Installation finale
│       ├── tasks/main.yml
│       └── templates/harbor.yml.j2
└── README.md                      # 📄 Cette doc
```


## 🚀 **Étapes Détaillées**

### **Étape 1 : VM Vagrant Debian 🖥️**

**Objectif** : VM légère (1Go RAM, 2CPU, 40Go disque) avec ports Harbor forwardés.

**Rôle** : Base système propre pour Docker/Harbor

```
Ports forwardés : 80→2200, 443→8443, 4443→4443, SSH→2222
IP fixe : 192.168.56.20
Firewall UFW : Ports Harbor ouverts
```

**Commande** : `vagrant up`
**Temps** : 3min

### **Étape 2 : Ansible Inventory 🔌**

**Objectif** : Connexion VM ↔ Ansible avec clé SSH Vagrant.

**Fichier clé** : `inventory/hosts`

```ini
[harbor]
harbor-vm ansible_host=192.168.56.20 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**Test** : `ansible -i inventory/hosts harbor-vm -m ping` → `"pong"`

**Problèmes courants** : SSH key permissions, host key verification

### **Étape 3 : Docker Engine 🐳**

**Objectif** : Docker CE 29.1.2 + Docker Compose v5.0.0.

**Rôle Ansible** : `roles/01-docker/tasks/main.yml`

```
- Repo Docker officiel
- docker-ce, docker-ce-cli, containerd.io, docker-compose-plugin
- User vagrant ∈ groupe docker (pas de sudo)
```

**Test** : `docker --version` (sans sudo)

### **Étape 4 : Téléchargement Harbor 📦**

**Objectif** : Harbor offline installer 2.11.1 (662Mo).

**Variables** : `group_vars/all.yml`

```yaml
harbor_version: "2.11.1"
harbor_home: "/home/vagrant/harbor"
harbor_hostname: "harbor.local"
harbor_admin_password: "Harbor12345"
```

**Rôle** : `roles/02-harbor-download`

```
- get_url : https://github.com/goharbor/harbor/releases/download/v2.11.1/harbor-offline-installer-v2.11.1.tgz
- unarchive → /home/vagrant/harbor/harbor/
```


### **Étape 5 : Certificats SSL 🔒**

**Objectif** : Certificats auto-signés pour `harbor.local` + Docker trust.

**Rôle** : `roles/03-certs`

```
sscg --hostname harbor.local --lifetime 365
→ /etc/ssl/certs/harbor.local.crt
→ /etc/ssl/private/harbor.local.key
→ /etc/docker/certs.d/harbor.local:443/ca.crt
```

**Debug** : `--hostname` (pas `--host`)

### **Étape 6 : Installation Finale 🎉**

**Objectif** : `./install.sh + docker compose up -d`

**Commande DEVOPS gagnante** :

```bash
cp harbor.yml.tmpl harbor.yml && sed -i 's/reg.mydomain.com/harbor.local/g' && sed -i '/https:/,/private_key:/s|certificate:.*|certificate: /etc/ssl/certs/harbor.local.crt|' && sed -i '/https:/,/private_key:/s|private_key:.*|private_key: /etc/ssl/private/harbor.local.key|' && sudo ./install.sh && docker compose up -d
```

**Résultat** :

```
✔ Harbor installed and started successfully
16 conteneurs UP : nginx, core, portal, registry, db, redis...
```


## 🌐 **ACCÈS HARBOR (PORTS VAGRANT)**

| Service | VM (Guest) | Host (Navigateur) | Protocole |
|---------|------------|-------------------|-----------|
| **UI Harbor** | 443 | [**https://localhost:8443**](https://localhost:8443) | HTTPS |
| **HTTP Redirect** | 80 | http://localhost:2200 | → HTTPS |
| **Docker Registry** | 443 | **localhost:8443** | HTTPS |
| **SSH** | 22 | 2222 | SSH |

### **ACCÈS DIRECT**



### **Docker Client**

```bash
docker login localhost:2200
# admin / Harbor12345

docker push localhost:2200/test-image:latest
```


## 🛠️ **Commandes Production**

```bash
# Status
cd /home/vagrant/harbor/harbor && docker compose ps

# Arrêt
docker compose down

# Redémarrage
docker compose up -d

# Logs
docker compose logs -f nginx
docker compose logs -f core
```


## 🔍 **Vérifications Techniques**

```bash
# API Harbor
curl http://localhost:2200/api/systeminfo

# Ressources
docker stats

# Disque
du -sh /home/vagrant/harbor/
```


## 📊 **Architecture Finale**

```
Vagrant VM (Debian Bookworm)
├── Docker 29.1.2 + Compose v5.0
├── Harbor 2.11.1 (16 conteneurs)
│   ├── nginx (proxy)
│   ├── harbor-core (API)
│   ├── harbor-portal (UI)
│   ├── registry (Docker registry)
│   ├── database (PostgreSQL)
│   ├── redis
│   └── trivy-adapter (vuln scanner)
└── 800Mo RAM / 2Go disque
```


## 🎓 **Concepts Pédagogiques**

1. **Vagrant** : VM as code, ports forwarding, synced folders
2. **Ansible** : Configuration management, rôles réutilisables, idempotence
3. **Docker** : Conteneurisation, compose multi-services
4. **Harbor** : Registry privé entreprise (scan vuln, signing, RBAC)
5. **DevOps** : Infrastructure as code, reproductibilité, documentation

## 🐛 **Problèmes \& Solutions**

| Problème | Cause | Solution |
| :-- | :-- | :-- |
| SSH Vagrant | Clé permissions | `chmod 600 .vagrant/.../private_key` |
| Ansible ping | Host key | `StrictHostKeyChecking=no` |
| Docker sudo | Groupe docker | `usermod -aG docker vagrant` |
| harbor.yml | Params manquants | Template officiel + sed |
| SSL certs | sscg syntaxe | `--hostname` (pas `--host`) |
| Ports collision | Vagrant auto-correct | `auto_correct: true` |

## 📈 **Ressources Consommées**

```
VM : 1Go RAM / 2 CPU / 40Go disque
Harbor : 800Mo RAM / 2Go disque / 16 conteneurs
Temps : 25min total
```


## 🎖️ **FÉLICITATIONS !**

**TP Harbor DevOps terminé à 100% ✅**

**Ouvre** : `http://localhost:2200`
**Login** : `admin/Harbor12345`

**Prochaine étape** : Push/pull images Docker, configuration projets, scan Trivy 🎉

**Auteur** : Guide automatisé Vagrant/Ansible/Harbor 2.11.1
**Date** : 09 Décembre 2025[^1][^2][^3]

<div align="center">⁂</div>

[^1]: https://github.com/goharbor/harbor/issues/10958

[^2]: https://github.com/goharbor/harbor/blob/main/make/harbor.yml.tmpl

[^3]: https://blog.csdn.net/qq_35700085/article/details/132226574

