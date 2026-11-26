# Énoncé : TP Avancés Switching et Routing (Partie A, Commutation)

> Source : [hackmd.io/@Formateur/SyLbZEmJQl](https://hackmd.io/@Formateur/SyLbZEmJQl), énoncé du formateur.
> Ce fichier reprend la **Partie A (TP1 à TP4)**, traitée dans ce dossier. La Partie B (TP5 à TP8 : routage statique, DHCP et relais, NAT/PAT, ACL) fera l'objet d'un autre dossier.
> Transcription structurée de la page (objectifs, consignes, livrables et critères repris point par point). La page d'origine fait foi.

---

## Contexte

- Chaque réseau représente une entreprise gérée par un ingénieur réseau.
- Plateforme : **PNETLab** (ou GNS3). Commutateurs Cisco **vIOS-L2** pour la commutation ; routeurs **c7200** pour le routage (Partie B).

## Mise en place

1. Télécharger la VM PNETLab au format OVA et l'importer dans VMware Workstation (VM déjà préparée, pas d'installation de PNETLab à faire).
2. Lancer la VM depuis VMware Workstation.
3. Accéder à l'interface web de PNETLab par son adresse IP (`http://PNETLAB_IP`).
4. Vérifier que cette IP répond au `ping` depuis l'invite de commandes de l'hôte.

---

## TP 1 : Spanning Tree Protocol (STP) et RSTP

**Objectif :** comprendre l'élection du pont racine, les rôles et états des ports, et la convergence rapide avec RSTP.

**Consignes :**
1. Concevoir une topologie de commutateurs redondants avec des boucles physiques.
2. Démontrer l'effet d'une boucle sans protection de couche 2.
3. Activer STP puis identifier le pont racine.
4. Passer la topologie en RSTP et mesurer le temps de convergence après la coupure d'un lien.
5. Comparer les états des ports avant et après la bascule.

**Livrables :**
- Schéma de la topologie, avec le rôle de chaque port.
- Tableau des ports : rôle (Root, Designated, Alternate) et état.
- Relevé des temps de convergence STP et RSTP.

**Validation :**
- Le pont racine est celui attendu par le plan.
- La coupure d'un lien est suivie d'une reprise de service.

---

## TP 2 : PVRSTP+ (Rapid PVST+) et répartition de charge par VLAN

**Objectif :** mettre en place une arborescence par VLAN pour utiliser tous les liens redondants.

**Consignes :**
1. Créer au moins deux VLAN et les transporter sur des trunks.
2. Configurer deux commutateurs racines différents selon les VLAN.
3. Vérifier que chaque VLAN emprunte un chemin différent.
4. Simuler la panne d'un commutateur racine et observer la reconvergence.

**Livrables :**
- Schéma avec les VLAN, les trunks et les racines par VLAN.
- Tableau de répartition des chemins par VLAN.
- Preuve de la reconvergence (sorties de commandes).

**Validation :**
- Chaque VLAN a un pont racine distinct.
- Aucun lien redondant n'est inutilisé sans raison.

---

## TP 3 : EtherChannel (LACP) et interaction avec STP

**Objectif :** agréger plusieurs liens physiques (minimum 8 liens) en un seul lien logique et comprendre l'effet sur STP.

**Consignes :**
1. Relier deux commutateurs par un maximum de liens.
2. Configurer un EtherChannel en LACP ou PAgP.
3. Vérifier que STP voit le groupe comme un seul lien.
4. Couper un des liens membres et constater la continuité du service.

**Livrables :**
- Schéma avec les liens agrégés et leur numérotation.
- Sorties des commandes de vérification de l'EtherChannel.
- Analyse courte de l'effet sur STP.

**Validation :**
- Le canal est actif avec tous ses membres.
- La panne d'un membre n'interrompt pas la communication.

---

## TP 4 : Sécurité de la commutation

**Objectif :** protéger la couche 2 contre les accès non autorisés et les erreurs de configuration.

**Consignes :**
1. Activer la sécurité des ports (port-security) sur les ports d'accès.
2. Activer BPDU Guard sur les ports d'extrémité et Root Guard sur les ports menant à la racine.
3. Tester en connectant un équipement non autorisé.
4. Activer DHCP Snooping et documenter son effet face à un faux serveur DHCP.

**Livrables :**
- Schéma indiquant les protections appliquées par port.
- Tableau de configuration des protections.
- Journal des tests et des événements de blocage.

**Validation :**
- Les ports non autorisés sont désactivés ou bloqués.
- Le trafic légitime n'est pas affecté.
