# Harbor TP - Étape 6 🎉 **INSTALLATION FINALE**

## 🎯 Objectif

Installation complète Harbor + démarrage services Docker Compose.

## 📁 Fichiers Créés

```
roles/04-harbor-install/
├── templates/
│   └── harbor.yml.j2     # Template SSL (optionnel)
│   └── tasks/
│       └── main.yml      # Installation + docker compose up
setup_harbor.yml          # Playbook MASTER (toutes étapes)
```


## ✅ **Ton Résultat - SUCCÈS TOTAL**

```
✔ ----Harbor has been installed and started successfully.----
Container harbor-log     Running ✅
Container harbor-db      Running ✅
Container harbor-portal  Running ✅
Container registry       Running ✅
Container redis          Running ✅
Container harbor-core    Running ✅
Container nginx          Running ✅
Container harbor-jobservice Started ✅
```


## 🔧 **Commande DEVOPS qui a marché**

```bash
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && cp harbor.yml.tmpl harbor.yml && sed -i 's/reg.mydomain.com/harbor.local/g' harbor.yml && sed -i '/https:/,/private_key:/s|certificate:.*|certificate: /etc/ssl/certs/harbor.local.crt|' harbor.yml && sed -i '/https:/,/private_key:/s|private_key:.*|private_key: /etc/ssl/private/harbor.local.key|' harbor.yml && sudo ./install.sh && docker compose up -d" -b
```


## 🌐 **ACCÈS HARBOR**

```
🌐 UI : http://localhost:2200
👤 Login : admin / Harbor12345
🐳 Docker login : docker login localhost:2200
```


## 🐛 **Problèmes Résolus (Debug)**

| Erreur | Cause | Fix |
| :-- | :-- | :-- |
| `max_job_workers` | Template incomplet | `jobservice.max_job_workers: 10` |
| `job_loggers` | Jobservice logger | `job_loggers: ["STD_OUTPUT", "FILE"]` |
| `logger_sweeper_duration` | Nettoyage logs | `logger_sweeper_duration: 1` |
| `webhook_job_http_client_timeout` | Notification webhook | Template officiel `harbor.yml.tmpl` + sed |
| `ssl_cert not set` | Certificats SSL | sed sur `https:` section |

## 📋 **Vérifications Post-Installation**

```bash
# Status services
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose ps" -b

# API Harbor
curl -k http://localhost:2200/api/systeminfo

# Logs nginx
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose logs nginx | tail -5" -b
```


## 📂 **Structure Projet Finale**

```
harbor-tp/                    ✅ 100% TERMINÉ
├── Vagrantfile              ✅ Étape 1
├── inventory/hosts          ✅ Étape 2
├── group_vars/all.yml       ✅ Étape 4
├── setup_harbor.yml         ✅ Étape 6
├── test-*.yml               ✅ Étapes 3-5
├── roles/
│   ├── 01-docker/           ✅ Docker 29.1.2
│   ├── 02-harbor-download/  ✅ Harbor 2.11.1
│   ├── 03-certs/            ✅ SSL harbor.local
│   └── 04-harbor-install/   ✅ Installation + up
└── README.md                ✅ Documentation
```


## 🚀 **Commandes Production**

```bash
# Arrêt
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose down" -b

# Redémarrage
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose up -d" -b

# Logs live
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose logs -f" -b
```


## 📊 **Ressources Utilisées**

| Composant | CPU | RAM | Disque |
| :-- | :-- | :-- | :-- |
| VM Vagrant | 2 | 1Go | 40Go |
| Harbor | 16 conteneurs | ~800Mo | 2Go |

## 🎖️ **STATUT GLOBAL TP**

```
✅ Étape 1-6 : 100% TERMINÉ
✅ Temps total : ~25min
✅ Harbor 2.11.1 fonctionnel
✅ DevOps reproductible
✅ Documentation complète
```

**TEST UI : http://localhost:2200** → **TP RÉUSSI** 🎉🚀[^1][^2][^3]

<div align="center">⁂</div>

[^1]: https://github.com/goharbor/harbor/issues/10958

[^2]: https://github.com/goharbor/harbor/blob/main/make/harbor.yml.tmpl

[^3]: https://blog.csdn.net/qq_35700085/article/details/132226574

