<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

## 🛑 **ÉTEINDRE HARBOR - 3 Méthodes**

### **1. HARBOR UNIQUEMENT** (Recommandé)

```bash
# Arrêt services Harbor
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose down" -b

# Vérif arrêt
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose ps" -b
```

**Résultat** : 16 conteneurs STOP → Ports 2200/8443 libres

### **2. HARBOR + Nettoyage Images** (Économie disque)

```bash
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose down -v && docker image prune -f" -b
```

**`-v`** : Supprime volumes DB/Redis
**`-f`** : Nettoie images orphelines

### **3. VM Complète** (Tout arrêter)

```bash
vagrant halt
# ou
vagrant destroy -f  # Supprime VM (réutilisable)
```


## **REDÉMARRAGE** (Après arrêt)

```bash
# Redémarrer Harbor
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose up -d" -b

# Vérif
curl -k https://localhost:8443/api/systeminfo
```


## **COMMANDES RÉSUMÉ** 📋

```bash
# 🛑 Arrêt Harbor
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose down" -b

# ▶️ Redémarrage
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose up -d" -b

# 📊 Status
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose ps" -b

# 🧹 Nettoyage total
ansible -i inventory/hosts harbor-vm -m shell -a "cd /home/vagrant/harbor/harbor && docker compose down -v && docker image prune -f" -b
```


## **EFFET SUR PORTS** 🔌

```
Avant arrêt : https://localhost:8443 → Harbor UI
Après arrêt  : Ports 2200/8443 libres
Après redémar : https://localhost:8443 → Harbor UP
```

**Exécute 1ère commande → Harbor éteint** 🛑

**VM reste allumée** → Redémarrage instantané 🚀

