# 🌐 TP réseau Cisco sur GNS3

![GNS3](https://img.shields.io/badge/GNS3-2.2.61-2E7D32?style=for-the-badge)
![Cisco IOS](https://img.shields.io/badge/Cisco_IOS-c7200_·_c3725-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![MobaXterm](https://img.shields.io/badge/MobaXterm-console-0D47A1?style=for-the-badge)
![SSH](https://img.shields.io/badge/SSH-v2-4D4D4D?style=for-the-badge&logo=openssh&logoColor=white)
![Spanning Tree](https://img.shields.io/badge/STP-802.1D-F57C00?style=for-the-badge)
![HackMD](https://img.shields.io/badge/Rendu-HackMD-333333?style=for-the-badge&logo=markdown&logoColor=white)

Travaux pratiques réseau réalisés au **Geneva Institute of Technology** (CFC informaticien), dans le cadre du PPE réseau (niveau CCNA).
Tous les TP utilisent **le même projet GNS3**, qui évolue d'un TP à l'autre au fil de la semaine.

---

## 📚 Les TP

| N° | TP | Ce qu'on construit | État | Dossier |
|---|---|---|---|---|
| 1 | Configuration de base Cisco | 4 routeurs + 2 switchs : nom, domaine, bannière, adressage IP, passerelles, comptes locaux, SSH v2, trunk 802.1Q | ✅ Terminé | [`tp1-config-base-cisco/`](tp1-config-base-cisco/) |
| 2 | Spanning Tree (STP / RSTP) | Triangle de 3 switchs : élection du pont racine, rôles des ports, coût, PortFast, BPDU Guard, pannes | ✅ Terminé | [`tp2-spanning-tree/`](tp2-spanning-tree/) |

Chaque dossier contient le rendu (`RENDU-HackMD.md`), les captures d'écran qui servent de preuves et, quand l'énoncé le demande, les configurations exportées.

---

## 🗂️ Organisation

```
tp-reseau-cisco-gns3/
├── tp1-config-base-cisco/
│   ├── RENDU-HackMD.md            ← rapport complet rendu au formateur
│   ├── enonce.md                  ← énoncé du TP
│   ├── TP_v2_GiocantiPaul.gns3    ← topologie GNS3 en fin de TP1
│   ├── captures/                  ← preuves (sorties des commandes show)
│   └── configs/                   ← configurations des 6 équipements
└── tp2-spanning-tree/
    ├── RENDU-HackMD.md
    ├── enonce.md
    └── captures/
```

---

## 🧰 Environnement

| Élément | Choix |
|---|---|
| Émulateur | GNS3 2.2.61 sur Windows, moteur Dynamips |
| Routeurs | Cisco 7200 (`c7200-adventerprisek9`, IOS 15.2), carte C7200-IO-FE |
| Switchs | EtherSwitch : routeur Cisco 3725 + module NM-16ESW (IOS 12.4) |
| Console | MobaXterm (imposé par le formateur) |

Les écarts avec les énoncés (switchs vIOS-L2 remplacés par des EtherSwitch, ports `Fa1/x` au lieu de `Gi0/x`, commandes non disponibles sur IOS 12.4) sont expliqués dans chaque rendu.

---

## 🔒 Sécurité

- Aucun mot de passe n'est publié : les valeurs sont **masquées** dans les fichiers `.cfg` et **pixelisées** sur les captures. Les `.cfg` publiés ne peuvent donc pas être rechargés tels quels sur un équipement.
- Les **images IOS Cisco** (logiciels sous licence) et les fichiers internes du projet GNS3 ne sont pas dans ce dépôt.

---

👤 **Paul Giocanti** · [GitHub @L4ami](https://github.com/L4ami)
