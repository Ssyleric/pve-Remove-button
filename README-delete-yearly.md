# 🧹 Suppression du job `yearly` invisible — Proxmox VE 8.4.5 (Bug GUI)

## 🎯 Objectif

Supprimer un **job de sauvegarde `yearly`** encore affiché dans **Datacenter > Backup**, alors qu’il **n’apparaît pas dans `/etc/pve/vzdump.cron`**, ni ne répond pas au bouton **"Remove"** dans l’interface Web Proxmox.

---

## 🐞 Problème identifié

- Le **job `yearly`** est visible dans l’onglet `Datacenter > Backup`
- Le **bouton "Remove" ne fonctionne pas** (aucune action, aucun log, aucun retour)
- Aucun job ne figure dans :
  ```bash
  cat /etc/pve/vzdump.cron
  ```
- ❗ Bug reconnu dans **Proxmox VE 8.4.x** :
  - [forum.proxmox.com - remove button not working](https://forum.proxmox.com/threads/remove-button-for-vm-operation-in-gui-not-working.162859)
  - [forum.proxmox.com - vzdump job ghost](https://forum.proxmox.com/threads/backup-job-persist-after-removal.123376/)
  - [bugzilla.proxmox.com issue #4883](https://bugzilla.proxmox.com/show_bug.cgi?id=4883)

---

## ✅ Procédure VSN utilisée

### 1. 🔍 Lister les jobs `vzdump` via l'API :

```bash
pvesh get /cluster/backup
```

**Retour exemple :**

```
backup-141dd42e-8105
backup-488a9abd-c43f   ← 👈 celui avec "schedule": "yearly"
backup-e8198075-0fed
```

---

### 2. 📋 Vérifier les détails de chaque job :

```bash
pvesh get /cluster/backup/backup-488a9abd-c43f
```

**Sortie confirmée :**

- `"schedule": "yearly"`
- `"vmid": "102,20232400"`
- `"storage": "pbs-marechal"`

---

### 3. 💣 Supprimer le job via l’API (VSN) :

```bash
pvesh delete /cluster/backup/backup-488a9abd-c43f
```

---

### 4. 🔁 Rafraîchissement :

- Recharger l’interface Web avec **`CTRL+F5`**
- Le job `yearly` disparaît définitivement

---

## 🧼 État final

| Élément             | Statut                            |
| --------------------- | --------------------------------- |
| `vzdump.cron`       | ✅ Vide                           |
| `yearly` visible    | ❌ Supprimé                      |
| Bouton Remove         | ❌ Inopérant (bug GUI confirmé) |
| Suppr API (`pvesh`) | ✅ Fonctionnelle (VSN)            |

---

## 🛡️ Statut : ✅ Nettoyage validé (VSN)

- Plus aucun job résiduel
- Pas d'impact sur les autres backups
- Utilisation API conforme aux bonnes pratiques Proxmox
