<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Harbor TP - Étape 5 🚀 (Certificats SSL Auto-signés)

## 🎯 Objectif

Générer certificats SSL auto-signés pour `harbor.local` + configuration Docker.

## 📁 Fichiers Créés

```
roles/03-certs/
├── tasks/
│   └── main.yml     # Génération sscg + Docker restart
test-certs.yml       # Playbook test
```


## 🔧 Contenu Rôle `03-certs`

```yaml
✅ Installe sscg (outil certificats)
✅ Crée répertoires SSL + Docker certs
✅ sscg --hostname harbor.local --lifetime 365
✅ Copie ca.crt → /etc/docker/certs.d/harbor.local:443/
✅ Redémarre Docker
✅ Vérifie 3 fichiers certs
```


## 💻 Commandes Exécution

```bash
ansible-playbook -i inventory/hosts test-certs.yml
```

**Ton résultat** ✅ :

```
TASK [03-certs : Display certificate status] 
"msg": "SSL certificates generated: True" (x3)
PLAY RECAP : ok=9 changed=3 failed=0
```


## 📋 Certificats Générés

| Fichier | Taille | Permissions | Statut |
| :-- | :-- | :-- | :-- |
| `/etc/ssl/certs/harbor.local.crt` | 1.6Ko | 0644 | ✅ |
| `/etc/ssl/private/harbor.local.key` | 1.7Ko | 0600 | ✅ |
| `/etc/docker/certs.d/harbor.local:443/ca.crt` | 1.6Ko | 0644 | ✅ |

## 🐛 Debug - Problèmes Résolus

### ❌ `Invalid option --host`

**Cause** : Syntaxe sscg incorrecte
**Fix** : `--hostname` + `--lifetime` + `--cert-file`

### ❌ `sscg non trouvé`

**Fix** : `apt install sscg`

## 📂 Structure Projet Actuelle

```
harbor-tp/
├── Vagrantfile              ✅ Étape 1
├── inventory/hosts          ✅ Étape 2
├── group_vars/all.yml       ✅ Étape 4
├── test-*.yml               ✅ Étapes 3-5
├── roles/
│   ├── 01-docker/           ✅
│   ├── 02-harbor-download/  ✅
│   └── 03-certs/            ✅ ← NOUVEAU
└── README.md                ✅
```


## ✅ Statut Étape 5

```
🟢 sscg installé
🟢 Certificats générés (365 jours)
🟢 Docker redémarré avec certs
🟢 Vérification 3 fichiers = True
```


## 🚀 Prochaine Étape

**Étape 6** : 🎉 **Installation Harbor Finale**

```
setup_harbor.yml → http://localhost:2200
```

**Temps** : 1min
**Espace** : +50Ko (certs)
**Statut global** : **5/6 ✅** → **83% complet** 🎉[^1]

<div align="center">⁂</div>

[^1]: paste.txt

