# 🔀 TP Avancés Switching : STP, Rapid PVST+, EtherChannel et sécurité L2

![PNETLab](https://img.shields.io/badge/PNETLab-VM_OVA-6A1B9A?style=for-the-badge)
![Cisco IOL](https://img.shields.io/badge/Cisco-IOL_L2-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![RSTP](https://img.shields.io/badge/STP-802.1D_·_RSTP-F57C00?style=for-the-badge)
![LACP](https://img.shields.io/badge/EtherChannel-LACP-2E7D32?style=for-the-badge)
![Security](https://img.shields.io/badge/L2_Security-Port--security_·_DHCP_Snooping-C62828?style=for-the-badge)
![HackMD](https://img.shields.io/badge/Rendu-HackMD-333333?style=for-the-badge&logo=markdown&logoColor=white)

Partie A (commutation) des **TP Avancés Switching et Routing** : quatre TP réalisés sur **PNETLab**, sur un même lab de 3 switchs Cisco IOL L2 montés en triangle.

| TP | Sujet | Ce qui est démontré |
|---|---|---|
| 1 | STP et RSTP | Tempête de broadcast sans STP (1 047 paquets/s), élection du root, rôles des ports, convergence 802.1D ≈ 48 s contre RSTP ≈ 6 s |
| 2 | Rapid PVST+ | VLAN 10 et 20 sur des trunks 802.1Q, un root par VLAN (partage de charge), reprise par le root de secours |
| 3 | EtherChannel LACP | 4 liens agrégés en Po1 (limite d'IOL), le STP ne voit qu'un lien, le service continue après la perte d'un membre |
| 4 | Sécurité L2 | Port-security, BPDU Guard, Root Guard, DHCP Snooping contre un faux serveur DHCP |

📄 Rapport complet : [`RENDU-HackMD.md`](RENDU-HackMD.md) · Énoncé : [`enonce.md`](enonce.md) · Preuves : [`captures/`](captures/)
