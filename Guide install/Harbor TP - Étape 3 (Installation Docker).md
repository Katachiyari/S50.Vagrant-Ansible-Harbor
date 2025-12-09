# Harbor TP - Étape 3 (Installation Docker)

## Objectif

Installer Docker Engine + Docker Compose sur la VM Debian pour Harbor.

## Fichiers Créés

```
roles/01-docker/
├── tasks/
│   └── main.yml     # Rôle Docker complet
test-docker.yml      # Playbook test
```


## Contenu `roles/01-docker/tasks/main.yml`

```yaml
- Update apt cache + dépendances Docker
- Ajoute clé GPG + repo Docker officiel
- Installe : docker-ce, docker-ce-cli, containerd.io, docker-compose-plugin
- Active service Docker + user vagrant dans groupe docker
- Redémarre Docker + wait 10s
```


## Contenu `test-docker.yml`

```yaml
---
- hosts: harbor
  become: yes
  roles:
    - 01-docker
```


## Commandes Exécution

```bash
# Copie le bloc complet de l'Étape 3
# Résultat attendu :
PLAY RECAP
harbor-vm : ok=9 changed=6 unreachable=0 failed=0
```


## Vérifications Post-Installation

```bash
ansible -i inventory/hosts harbor-vm -a "docker --version" -b
ansible -i inventory/hosts harbor-vm -a "docker compose version" -b
ansible -i inventory/hosts harbor-vm -a "groups" -b | grep docker
```

**Résultats attendus** :

```
docker version 27.x.x
Docker Compose version v2.x.x
vagrant : ... docker
```


## Debug - Problèmes Courants

### ❌ Erreur YAML `apt: is not a valid attribute`

**Cause** : Indentation YAML (2 espaces)

```
❌ - name: Install
    apt: name: docker-ce     # Mauvais
✅ - name: Install
  apt:
    name: docker-ce         # Correct
```


### ❌ Erreur Jinja2 `unexpected '.'`

**Cause** : `{{.ServerVersion}}` dans shell
**Solution** : Supprimé du rôle final

### ❌ `Permission denied (publickey)`

**Vérif** : `inventory/hosts` contient `ansible_ssh_private_key_file`

## Structure Projet Actuelle

```
harbor-tp/
├── Vagrantfile      ✅ Étape 1
├── inventory/hosts  ✅ Étape 2
├── test-docker.yml  ✅ Étape 3
├── roles/
│   └── 01-docker/   ✅ Étape 3
│       └── tasks/
│           └── main.yml
└── README.md        ✅
```


## Statut Étape 3

```
✅ PLAY RECAP ok=9+ changed=6+ failed=0 → Docker installé
✅ docker --version OK
✅ vagrant dans groupe docker
```


## Prochaine Étape

**Étape 4** : Téléchargement Harbor offline installer

**Temps** : 3min
**Résultat** : Docker prêt pour Harbor[^1][^2]

<div align="center">⁂</div>

[^1]: https://www.youtube.com/watch?v=BDw8Ux61TgQ

[^2]: paste.txt

