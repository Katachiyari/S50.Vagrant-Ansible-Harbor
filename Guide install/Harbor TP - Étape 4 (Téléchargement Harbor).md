# Harbor TP - Étape 4 (Téléchargement Harbor)

## Objectif

Télécharger et extraire Harbor offline installer dans `/home/vagrant/harbor`.

## Fichiers Créés

```
group_vars/all.yml                    # Variables globales Harbor
roles/02-harbor-download/tasks/main.yml # Rôle téléchargement
test-harbor-download.yml              # Playbook test
```


## Variables `group_vars/all.yml`

```yaml
harbor_version: "2.11.1"
harbor_filename: "harbor-offline-installer-v2.11.1.tgz"
harbor_url: "https://github.com/goharbor/harbor/releases/download/v2.11.1/harbor-offline-installer-v2.11.1.tgz"
harbor_home: "/home/vagrant/harbor"
harbor_hostname: "harbor.local"
harbor_admin_password: "Harbor123!"
```


## Contenu Rôle `02-harbor-download`

```yaml
- Crée /home/vagrant/harbor (vagrant:vagrant)
- Télécharge harbor-offline-installer-v2.11.1.tgz
- Extrait dans /home/vagrant/harbor/harbor/
- Permissions 0755 récursives
```


## Commandes Exécution

```bash
ansible-playbook -i inventory/hosts test-harbor-download.yml
```

**Résultat attendu** (le tien) :

```
TASK [02-harbor-download : Show Harbor version] 
ok: [harbor-vm] => {
    "msg": "Harbor 2.11.1 downloaded to /home/vagrant/harbor"
}
PLAY RECAP
harbor-vm : ok=7 changed=5 unreachable=0 failed=0
```


## Vérifications Post-Installation

```bash
ansible -i inventory/hosts harbor-vm -a "ls -la /home/vagrant/harbor/harbor"
```

**Doit afficher** : `docker-compose.yml  harbor.yml  install.sh  prepare`

## Debug - Problèmes Courants

### ❌ `404 Not Found` (version Harbor)

**Solution** : Vérifie `harbor_version` sur https://github.com/goharbor/harbor/releases

### ❌ `Permission denied`

**Solution** : `become: yes` dans playbook + `owner: vagrant`

### ❌ `unarchive failed`

**Solution** : Fichier `.tgz` téléchargé complètement (`register: harbor_download`)

## Structure Projet Actuelle

```
harbor-tp/
├── Vagrantfile              ✅ Étape 1
├── inventory/hosts          ✅ Étape 2
├── group_vars/
│   └── all.yml              ✅ Étape 4
├── test-docker.yml          ✅ Étape 3
├── test-harbor-download.yml ✅ Étape 4
├── roles/
│   ├── 01-docker/           ✅ Étape 3
│   └── 02-harbor-download/  ✅ Étape 4
│       └── tasks/
│           └── main.yml
└── README.md                ✅
```


## Statut Étape 4

```
✅ Harbor 2.11.1 téléchargé (400+ Mo)
✅ Extrait dans /home/vagrant/harbor/harbor/
✅ Permissions vagrant:vagrant OK
```


## Prochaine Étape

**Étape 5** : Génération certificats SSL auto-signés

**Temps** : 2min
**Espace disque** : +450Mo
**Statut global** : 4/6 étapes ✅[^1][^2]

<div align="center">⁂</div>

[^1]: paste.txt

[^2]: https://middlewaretechnologies.in/2024/11/how-to-setup-harbor-registry-using-ansible-playbook.html

