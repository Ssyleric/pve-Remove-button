# ✅ Suppression d’une tâche de backup planifiée impossible à supprimer via l’interface Proxmox

## 🎯 Objectif

Supprimer proprement une tâche de backup planifiée qui apparaît dans l’interface graphique Proxmox, mais dont le bouton **Remove** est inopérant ou grisé.

---

## 📌 Contexte vérifié

- Proxmox VE 8.4.5
- Aucun fichier dans :
  - `/etc/pve/vzdump.cron`
  - `/etc/pve/jobs/`
  - `/etc/pve/datacenter.cfg.d/`
- Tâches de backup visibles uniquement via l’API `pvesh`

---

## 🧪 Commandes exécutées

```bash
nano /etc/pve/vzdump.cron
ls -l /etc/pve/jobs/
ls -l /etc/pve/datacenter.cfg.d/
```

---

## 🔍 Détection des tâches via API

```bash
pvesh get /cluster/backup
```

| Tâche ID            |
| -------------------- |
| backup-141dd42e-8105 |
| backup-5b65201d-f95c |
| backup-e8198075-0fed |

```

---

## 📄 Détail de chaque tâche

### backup-141dd42e-8105

| Clé              | Valeur                                           |
|------------------|--------------------------------------------------|
| enabled          | 1                                                |
| fleecing         | {"enabled":"0"}                                  |
| id               | backup-141dd42e-8105                             |
| mailnotification | always                                           |
| mailto           | XXXXXXXXXXXX@gmail.com                           |
| mode             | snapshot                                         |
| node             | pve                                              |
| notes-template   | {{vmid}} {{guestname}}, {{node}}, {{cluster}}    |
| repeat-missed    | 1                                                |
| schedule         | mon,thu 02:30                                    |
| storage          | pbs-xxxxxxxx                                     |
| type             | vzdump                                           |
| vmid             | 103,101,102,104                                  |

---

### backup-5b65201d-f95c

| Clé               | Valeur                     |
|-------------------|----------------------------|
| enabled           | 1                          |
| fleecing          | {"enabled":"0"}            |
| id                | backup-5b65201d-f95c       |
| mailnotification  | always                     |
| mailto            | XXXXXXXXXXXX@gmail.com     |
| mode              | stop                       |
| notes-template    | {{guestname}}              |
| notification-mode | legacy-sendmail            |
| schedule          | yearly                     |
| storage           | pbs-xxxxxxxx               |
| type              | vzdump                     |
| vmid              | 20232400,20308096          |

---

### backup-e8198075-0fed

| Clé              | Valeur                                           |
|------------------|--------------------------------------------------|
| enabled          | 1                                                |
| fleecing         | {"enabled":"0"}                                  |
| id               | backup-e8198075-0fed                             |
| mailnotification | always                                           |
| mailto           | XXXXXXXXXXXX@gmail.com                           |
| mode             | stop                                             |
| node             | pve                                              |
| notes-template   | {{vmid}} {{guestname}}, {{node}}, {{cluster}}    |
| repeat-missed    | 1                                                |
| schedule         | mon,thu 04:00                                    |
| storage          | pbs-xxxxxxxx                                     |
| type             | vzdump                                           |
| vmid             | 20308096,20009000,20232400,20401080,20508080     |

---

## 🧹 Suppression de la tâche "yearly" (impossible via GUI)

```bash
pvesh delete /cluster/backup/backup-5b65201d-f95c
```

✅ Tâche supprimée avec succès :

```bash
pvesh get /cluster/backup
```

| Tâche ID            |
| -------------------- |
| backup-141dd42e-8105 |
| backup-e8198075-0fed |

```

```
