<!-- Source : https://hackmd.io/@Formateur/HkrYKYbJml — copié le 23.11.2026. Texte intégral de la page ; mise en forme Markdown reconstituée depuis la page affichée (tableaux, blocs de commandes). -->

# TP : Attribution des interfaces et des adresses IP, configuration de base Cisco

###### tags: `GIT` `Satom` `GNS3` `Cisco` `TP` `FastEthernet` `SSH` `/20`

> **Geneva Institute of Technology · Satom IT & Learning Solutions**
> Version 2 : attribution des interfaces et IP refaite, intégration des notions du cours « Configuration d'un routeur Cisco ».

---
![image](https://hackmd.io/_uploads/By3yjK-ymg.png)

---

## 1. Ce qui change par rapport à la version 1

| Élément | Version 1 | Version 2 |
|---|---|---|
| Interface LAN des routeurs | `GigabitEthernet0/0` | `FastEthernet0/0` (routeurs c7206) |
| Attribution des IP | Une adresse par routeur, sans plan détaillé | Tableau d'attribution par équipement et par interface |
| Passerelle des routeurs | Non traitée | `ip route 0.0.0.0 0.0.0.0 192.168.10.1` sur R2, R3, R4 |
| Mots de passe | `enable secret`, `login local` | Ajout de `login`/`password` sur les lignes, comme dans le cours |
| Temporisation | `exec-timeout 10 0` | `exec-timeout 10 30` (10 min 30 s) |
| Services web | Non traités | `no ip http server` et `no ip http secure-server` |
| Vérifications | `show ip interface brief` | Ajout de `show run interface`, `show run \| include ip route`, `show line`, `show users` |
| Suppression | Non traitée | Commandes `no` pour revenir en arrière |

---

## 2. Objectifs

1. Attribuer à chaque équipement une interface et une adresse IP selon le plan imposé.
2. Configurer le nom, le domaine et la bannière de chaque équipement.
3. Activer les interfaces (`no shutdown`) et vérifier leur état.
4. Configurer une passerelle par défaut sur les routeurs qui ne sont pas directement sur le même réseau que la passerelle.
5. Sécuriser les accès console, VTY et mode privilégié.
6. Activer SSH v2, désactiver Telnet et les interfaces web.
7. Vérifier chaque étape avec les commandes `show`, puis savoir annuler une commande avec `no`.

## 3. Topologie et câblage

(Schéma de la page : R1 Fa0/0 · 192.168.10.1 et R2 Fa0/0 · 192.168.10.2 reliés à SW1 vIOS-L2 Vlan1 · 192.168.10.11 ; R3 Fa0/0 · 192.168.10.3 et R4 Fa0/0 · 192.168.10.4 reliés à SW2 vIOS-L2 Vlan1 · 192.168.10.12 ; L1 : Fa0/0 ↔ Gi0/1, L2 : Fa0/0 ↔ Gi0/2, L3 : Fa0/0 ↔ Gi0/1, L4 : Fa0/0 ↔ Gi0/2, L5 : Gi0/15 ↔ Gi0/15 Trunk 802.1Q)

| N° | Équipement A | Interface A | Équipement B | Interface B | Type |
|---|---|---|---|---|---|
| L1 | R1 | Fa0/0 | SW1 | Gi0/1 | Accès |
| L2 | R2 | Fa0/0 | SW1 | Gi0/2 | Accès |
| L3 | R3 | Fa0/0 | SW2 | Gi0/1 | Accès |
| L4 | R4 | Fa0/0 | SW2 | Gi0/2 | Accès |
| L5 | SW1 | Gi0/15 | SW2 | Gi0/15 | Trunk 802.1Q |

## 4. Attribution des interfaces et des adresses IP

### 4.1 Plan d'adressage

| Paramètre | Valeur |
|---|---|
| Réseau | 192.168.10.0/24 |
| Masque | 255.255.255.0 |
| Passerelle du LAN | 192.168.10.1 (R1) |
| Domaine | git.satom.ch |

### 4.2 Tableau d'attribution par équipement

| Équipement | Rôle | Interface | Adresse IP | Masque | Passerelle par défaut | Description à saisir |
|---|---|---|---|---|---|---|
| R1 | Passerelle du LAN | FastEthernet0/0 | 192.168.10.1 | 255.255.255.0 | — (aucune) | LAN vers SW1 Gi0/1 (L1) |
| R2 | Routeur | FastEthernet0/0 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 | LAN vers SW1 Gi0/2 (L2) |
| R3 | Routeur | FastEthernet0/0 | 192.168.10.3 | 255.255.255.0 | 192.168.10.1 | LAN vers SW2 Gi0/1 (L3) |
| R4 | Routeur | FastEthernet0/0 | 192.168.10.4 | 255.255.255.0 | 192.168.10.1 | LAN vers SW2 Gi0/2 (L4) |
| SW1 | Switch de gestion | Vlan1 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 (ip default-gateway) | Gestion SW1 |
| SW2 | Switch de gestion | Vlan1 | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 (ip default-gateway) | Gestion SW2 |

### 4.3 Tableau d'attribution des ports des switches

| Switch | Port | Usage | Mode | VLAN |
|---|---|---|---|---|
| SW1 | Gi0/1 | R1 Fa0/0 | access | 1 |
| SW1 | Gi0/2 | R2 Fa0/0 | access | 1 |
| SW1 | Gi0/3 à Gi0/14 | Non utilisés | désactivés (shutdown) | — |
| SW1 | Gi0/15 | Trunk vers SW2 | trunk 802.1Q | natif 1 |
| SW2 | Gi0/1 | R3 Fa0/0 | access | 1 |
| SW2 | Gi0/2 | R4 Fa0/0 | access | 1 |
| SW2 | Gi0/3 à Gi0/14 | Non utilisés | désactivés (shutdown) | — |
| SW2 | Gi0/15 | Trunk vers SW1 | trunk 802.1Q | natif 1 |

> ⚠️ Vérifie les noms d'interfaces dès le départ : `show ip interface brief` sur un routeur, `show interfaces status` sur un switch.

## 5. Notions du cours intégrées

| Notion | Commande | Pourquoi |
|---|---|---|
| Nom du routeur | `hostname R1` | Identifier l'équipement |
| Nom de domaine | `ip domain-name git.satom.ch` | Obligatoire pour générer la clé SSH |
| Suppression du nom | `no hostname`, `no ip domain-name` | Annuler une mauvaise saisie |
| Adresse IP | `ip address 192.168.10.x 255.255.255.0` | Donner une identité réseau à l'interface |
| Suppression d'une IP | `no ip address` | Retirer une adresse erronée |
| Activation | `no shutdown` | Les interfaces sont désactivées par défaut |
| Désactivation | `shutdown` | Couper proprement un port |
| Passerelle par défaut | `ip route 0.0.0.0 0.0.0.0 192.168.10.1` | Atteindre les réseaux inconnus via R1 |
| Chiffrement des mots de passe | `service password-encryption` | Empêcher la lecture des mots de passe en clair |
| Mot de passe privilégié | `enable secret` | Protéger le mode enable |
| Mot de passe console | `line con 0` + `password` + `login` | Protéger l'accès physique |
| Mot de passe VTY | `line vty 0 4` + `password` + `login` | Protéger l'accès distant |
| Compte local | `username admin privilege 15 secret` | Authentification nominative pour SSH |
| Temporisation | `exec-timeout 10 30` | Fermer les sessions inactives |
| Bannière | `banner motd #...#` | Message d'avertissement à la connexion |
| Suppression bannière | `no banner motd` | Annuler la bannière |
| Interface web | `no ip http server`, `no ip http secure-server` | Fermer les accès web du routeur |
| Accès distant sécurisé | `ip ssh version 2`, `transport input ssh` | SSH v2 seul, Telnet interdit |

**Différence avec le cours** : dans le PDF, les lignes VTY reçoivent un simple `password` et `login`. Dans ce TP, on ajoute `login local` et `username admin` pour une authentification nominative, plus propre et requise pour SSH.

## 6. Travail à réaliser

### Étape 1 : Démarrage et vérification des interfaces (2 points)

1. Câbler la topologie du chapitre 3 dans GNS3.
2. Démarrer les nœuds et ouvrir les consoles.
3. Sur chaque routeur, exécuter :

```
enable
show ip interface brief
```

Noter le nom réel de l'interface LAN. Si elle n'est pas `FastEthernet0/0`, signaler l'écart à l'enseignant avant de continuer.

**Livrable** : capture de `show ip interface brief` sur R1.

### Étape 2 : Nom, domaine et bannière (3 points)

Sur les six équipements :

```
configure terminal
hostname R1
ip domain-name git.satom.ch
no ip domain-lookup
banner motd #
Acces reserve au personnel autorise.
Toute connexion non autorisee est interdite.
#
end
```

Remplacer `R1` par le nom de chaque équipement.

**Question 2.1** : pourquoi le `ip domain-name` doit-il être défini avant `crypto key generate rsa` ?

**Question 2.2** : à quoi sert la commande `no hostname` et quel est son effet sur le prompt ?

**Livrable** : `show running-config | include hostname|domain|banner` sur les six équipements.

### Étape 3 : Adressage IP des routeurs (3 points)

Sur R1 à R4, saisir l'interface et l'adresse selon le tableau 4.2. Exemple pour R2 :

```
configure terminal
interface FastEthernet0/0
description LAN vers SW1 Gi0/2 (L2)
ip address 192.168.10.2 255.255.255.0
no shutdown
end
```

Vérifier ensuite :

```
show run interface FastEthernet0/0
show ip interface brief
```

**Question 3.1** : quelle est la différence entre `shutdown` et l'absence d'adresse IP sur une interface ?

**Livrable** : `show run interface FastEthernet0/0` sur R2.

### Étape 4 : Adressage IP des switches (2 points)

Sur SW1 et SW2 :

```
configure terminal
interface Vlan1
description Gestion SW1
ip address 192.168.10.11 255.255.255.0
no shutdown
!
ip default-gateway 192.168.10.1
end
```

Remplacer `192.168.10.11` par `192.168.10.12` sur SW2 et adapter la description.

**Question 4.1** : pourquoi `ip default-gateway` est-il nécessaire sur un switch et pas sur R1 ?

**Livrable** : `show ip interface brief` et `show ip default-gateway` sur SW1.

### Étape 5 : Passerelle par défaut des routeurs R2, R3 et R4 (2 points)

Sur R2, R3 et R4 (mais pas sur R1, qui est déjà la passerelle) :

```
configure terminal
ip route 0.0.0.0 0.0.0.0 192.168.10.1
end
```

Vérifier :

```
show run | include ip route
```

**Question 5.1** : que signifie la route `0.0.0.0 0.0.0.0` ? Quel trafic emprunte-t-elle ?

**Livrable** : `show run | include ip route` sur R3.

### Étape 6 : Connectivité (2 points)

Depuis R2 :

```
ping 192.168.10.1
ping 192.168.10.3
ping 192.168.10.4
ping 192.168.10.11
ping 192.168.10.12
```

Les cinq pings doivent répondre `!!!!!`.

**Livrable** : capture d'un ping réussi vers SW2 (192.168.10.12).

### Étape 7 : Mots de passe et lignes d'accès (3 points)

Sur les six équipements :

```
configure terminal
service password-encryption
enable secret <MOT_DE_PASSE_ENABLE>
username admin privilege 15 secret <MOT_DE_PASSE_ADMIN>
!
line con 0
password <MOT_DE_PASSE_CONSOLE>
login local
exec-timeout 10 30
logging synchronous
!
line vty 0 4
password <MOT_DE_PASSE_VTY>
login local
exec-timeout 10 30
logging synchronous
transport input ssh
end
```

Les valeurs `<...>` sont à choisir par l'étudiant : 12 caractères minimum, majuscules, minuscules, chiffres et symboles. Ne pas réutiliser les exemples du cours.

Vérifier :

```
show running-config | section line
show users
```

**Question 7.1** : pourquoi `service password-encryption` ne chiffre-t-il pas la valeur de `enable secret` ? Quel est le rôle réel de cette commande ?

**Question 7.2** : dans le cours, les lignes VTY n'ont qu'un `password`. Quelle faiblesse cela introduit-il par rapport à `login local` avec un compte nominatif ?

**Livrable** : `show running-config | section line` sur R1 et `show users` pendant une session console.

### Étape 8 : SSH v2, désactivation de Telnet et des interfaces web (4 points)

Sur les six équipements :

```
configure terminal
ip domain-name git.satom.ch
crypto key generate rsa modulus 2048
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 3
no ip http server
no ip http secure-server
end
```

**Question 8.1** : pourquoi désactiver `ip http server` ? Quel risque cela supprime-t-il ?

**Question 8.2** : que se passe-t-il si `transport input ssh` est absent ? Telnet reste-t-il possible ?

**Livrables** :
- `show ip ssh` sur R1 (`SSH Enabled - version 2.0`) ;
- `show ip http server status` sur R1 (serveur HTTP désactivé).

### Étape 9 : Trunk 802.1Q et ports des switches (3 points)

Sur SW1 et SW2 :

```
configure terminal
interface range GigabitEthernet0/1 - 2
switchport mode access
switchport access vlan 1
no shutdown
!
interface GigabitEthernet0/15
switchport mode trunk
switchport trunk native vlan 1
no shutdown
!
interface range GigabitEthernet0/3 - 14
shutdown
end
```

**Question 9.1** : quelle est la différence entre un port access et un port trunk ?

**Question 9.2** : pourquoi le VLAN natif doit-il être identique aux deux extrémités du trunk ?

**Livrable** : `show interfaces trunk` sur SW1 (Gi0/15 en `trunking`).

### Étape 10 : Tests d'accès et démonstration (4 points)

Depuis R2 :

1. `ssh -l admin 192.168.10.1` : connexion réussie, bannière affichée.
2. `ssh -l admin 192.168.10.11` : connexion réussie sur SW1.
3. `telnet 192.168.10.1` : refusé.
4. Trois mauvais mots de passe SSH sur R1 : la quatrième tentative est bloquée.

**Livrables** : captures des quatre tests.

### Étape 11 : Sauvegarde et annulation (2 points)

1. Sauvegarder sur chaque équipement :

```
write memory
```

2. Démontrer une annulation sur R2 : supprimer puis rétablir l'adresse IP.

```
configure terminal
interface FastEthernet0/0
no ip address
ip address 192.168.10.2 255.255.255.0
end
```

**Question 11.1** : pourquoi `write memory` est-il indispensable après une annulation ?

**Livrable** : `show run interface FastEthernet0/0` avant et après l'annulation.

## 7. Livrables à rendre

| N° | Livrable |
|---|---|
| 1 | Projet GNS3 sauvegardé (`TP_v2_NomPrenom.gns3`) |
| 2 | Captures des `show` demandés à chaque étape |
| 3 | Fichiers `.cfg` exportés de R1 à R4, SW1, SW2 |
| 4 | Réponses aux questions 2.1 à 11.1 |

## 8. Barème (sur 20)

| Étape | Thème | Points |
|---|---|---|
| 1 | Démarrage et interfaces | 2 |
| 2 | Nom, domaine, bannière | 3 |
| 3 | Adressage IP des routeurs | 3 |
| 4 | Adressage IP des switches | 2 |
| 5 | Passerelle par défaut | 2 |
| 6 | Connectivité | 2 |
| 7 | Mots de passe et lignes | 3 |
| 8 | SSH v2, Telnet et HTTP | 4 |
| 9 | Trunk et ports | 3 |
| 10 | Tests d'accès | 4 |
| 11 | Sauvegarde et annulation | 2 |
| | **Total brut** | **30** |

Note sur 20 = total obtenu × 20 / 30.

## 9. Aide-mémoire

| Besoin | Commande |
|---|---|
| Voir les interfaces | `show ip interface brief` |
| Voir une interface | `show run interface FastEthernet0/0` |
| Voir les routes statiques | `show run \| include ip route` |
| Voir les lignes | `show line` |
| Voir les connexions | `show users` |
| Voir la clé SSH | `show crypto key mypubkey rsa` |
| Voir le trunk | `show interfaces trunk` |
| Annuler une commande | `no <commande>` |
| Sauvegarder | `write memory` |

*TP v2 rédigé pour GIT · Satom IT & Learning Solutions · Quai du Seujet 10 – 1201 Geneva · www.satom.ch*
