# 🧹 Suppression du job `yearly` invisible — Proxmox VE 8.4.5 (Bug GUI)

📘 README — Suppression d’une tâche de backup planifiée dans Proxmox VE 

🎯 Objectif
Supprimer une tâche de sauvegarde planifiée visible dans l’interface Proxmox, mais impossible à supprimer via le bouton `Remove` (grisé ou inactif).

✅ Contexte utilisateur vérifié
- Version Proxmox VE : 8.4.5
- Nom du stockage utilisé : pbs-xxxxxxxx
- Tâches visibles dans l’interface, mais aucune entrée dans :
  - /etc/pve/vzdump.cron
  - /etc/pve/jobs/
  - /etc/pve/datacenter.cfg.d/
- Seule méthode fonctionnelle = interrogation via API `pvesh`.

🧪 Commandes exécutées

nano /etc/pve/vzdump.cron
ls -l /etc/pve/jobs/
# → ls: cannot access '/etc/pve/jobs/': No such file or directory
ls -l /etc/pve/datacenter.cfg.d/
# → ls: cannot access '/etc/pve/datacenter.cfg.d/': No such file or directory

🔍 Tâches détectées via l’API

pvesh get /cluster/backup
┌──────────────────────┐
│ id                   │
╞══════════════════════╡
│ backup-141dd42e-8105 │
├──────────────────────┤
│ backup-5b65201d-f95c │
├──────────────────────┤
│ backup-e8198075-0fed │
└──────────────────────┘

📄 Contenu exact des 3 tâches

pvesh get /cluster/backup/backup-141dd42e-8105
┌──────────────────┬───────────────────────────────────────────────┐
│ key              │ value                                         │
╞══════════════════╪═══════════════════════════════════════════════╡
│ enabled          │ 1                                             │
│ fleecing         │ {"enabled":"0"}                               │
│ id               │ backup-141dd42e-8105                          │
│ mailnotification │ always                                        │
│ mailto           │ XXXXXXXXXXXX@gmail.com                        │
│ mode             │ snapshot                                      │
│ node             │ pve                                           │
│ notes-template   │ {{vmid}} {{guestname}}, {{node}}, {{cluster}} │
│ repeat-missed    │ 1                                             │
│ schedule         │ mon,thu 02:30                                 │
│ storage          │ pbs-xxxxxxxx                                  │
│ type             │ vzdump                                        │
│ vmid             │ 103,101,102,104                               │
└──────────────────┴───────────────────────────────────────────────┘

pvesh get /cluster/backup/backup-5b65201d-f95c
┌───────────────────┬────────────────────────┐
│ key               │ value                  │
╞═══════════════════╪════════════════════════╡
│ enabled           │ 1                      │
│ fleecing          │ {"enabled":"0"}        │
│ id                │ backup-5b65201d-f95c   │
│ mailnotification  │ always                 │
│ mailto            │ XXXXXXXXXXXX@gmail.com │
│ mode              │ stop                   │
│ notes-template    │ {{guestname}}          │
│ notification-mode │ legacy-sendmail        │
│ schedule          │ yearly                 │
│ storage           │ pbs-xxxxxxxx           │
│ type              │ vzdump                 │
│ vmid              │ 20232400,20308096      │
└───────────────────┴────────────────────────┘

pvesh get /cluster/backup/backup-e8198075-0fed
┌──────────────────┬───────────────────────────────────────────────┐
│ key              │ value                                         │
╞══════════════════╪═══════════════════════════════════════════════╡
│ enabled          │ 1                                             │
│ fleecing         │ {"enabled":"0"}                               │
│ id               │ backup-e8198075-0fed                          │
│ mailnotification │ always                                        │
│ mailto           │ XXXXXXXXXXXX@gmail.com                        │
│ mode             │ stop                                          │
│ node             │ pve                                           │
│ notes-template   │ {{vmid}} {{guestname}}, {{node}}, {{cluster}} │
│ repeat-missed    │ 1                                             │
│ schedule         │ mon,thu 04:00                                 │
│ storage          │ pbs-xxxxxxxx                                  │
│ type             │ vzdump                                        │
│ vmid             │ 20308096,20009000,20232400,20401080,20508080  │
└──────────────────┴───────────────────────────────────────────────┘

🗑️ Suppression de la tâche `yearly`

pvesh delete /cluster/backup/backup-5b65201d-f95c

Vérification :

pvesh get /cluster/backup
┌──────────────────────┐
│ id                   │
╞══════════════════════╡
│ backup-141dd42e-8105 │
├──────────────────────┤
│ backup-e8198075-0fed │
└──────────────────────┘

✅ La tâche backup-5b65201d-f95c a bien été supprimée avec effet immédiat.

📌 Résumé final

| ID                       | Schedule      | Mode     | VMIDs                               |
|--------------------------|---------------|----------|-------------------------------------|
| backup-141dd42e-8105     | Mon,Thu 02:30 | snapshot | 103,101,102,104                     |
| backup-e8198075-0fed     | Mon,Thu 04:00 | stop     | 20308096,20009000,20232400,...      |
