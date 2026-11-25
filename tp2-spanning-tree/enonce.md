<!-- Source : https://hackmd.io/@Formateur/S1Om20fkXl — reçu le 24.11.2026. La page HackMD ne contient que 18 images : texte intégral transcrit depuis ces images, dans l'ordre. -->

# TP — Spanning Tree Protocol (STP / RSTP)

*Geneva Institute of Technology · Satom IT & Learning Solutions*
*Topologie à 3 commutateurs en triangle · Niveau : CCNA / CFC Informaticien · Durée estimée : 3 h*

## 1. Objectifs pédagogiques

À l'issue du TP, l'étudiant sera capable de :
- expliquer pourquoi une topologie redondante en triangle crée des **boucles de commutation** et une **tempête de broadcast** sans protocole de contrôle ;
- déterminer le **pont racine** (root bridge) à partir de l'identifiant de pont (BID) ;
- identifier le rôle de chaque port (*Root*, *Designated*, *Alternate / Blocked*) et prédire le port qui sera bloqué ;
- influencer l'élection avec la priorité et le coût de port ;
- sécuriser les ports d'accès avec **PortFast** et **BPDU Guard** ;
- mesurer la convergence après une panne, puis comparer STP (802.1D) et RSTP (802.1w).

## 2. Rappel théorique

### 2.1 Le problème : la boucle de commutation
Dans un réseau de niveau 2, les commutateurs **inondent** (flooding) les trames de diffusion (broadcast, ex. ARP) et les trames à destination inconnue. Dans un triangle physique ESW1 – ESW2 – ESW3, une trame de broadcast repart indéfiniment dans la boucle. Comme la couche 2 n'a pas de champ TTL, rien ne l'arrête. Conséquences :
- **Tempête de broadcast** : saturation des liens et du CPU des commutateurs ;
- **Instabilité de la table MAC** : une même adresse est apprise sur plusieurs ports successivement ;
- **Duplication de trames** : un hôte reçoit plusieurs copies d'une même trame unicast.

> **Solution :** le Spanning Tree Protocol désactive logiquement certains liens (état *blocking*) en gardant une topologie sans boucle, tout en laissant le lien redondant prêt à prendre le relais.

### 2.2 Identifiant de pont (BID) et élection du root
Chaque commutateur envoie des **BPDU** (Bridge Protocol Data Units). Le BID est composé de :

| Composant | Taille | Valeur par défaut | Rôle |
|---|---|---|---|
| Priorité | 4 bits (multiple de 4096) | 32768 | Plus la valeur est **basse**, plus le commutateur est prioritaire |
| Extended System ID | 12 bits | = numéro de VLAN (ici 1) | Permet un STP par VLAN (PVST+) |
| Adresse MAC | 48 bits | MAC de base du commutateur | Départage en cas d'égalité de priorité |

Le **pont racine** est le commutateur dont le BID est le plus bas. Tous les autres calculent leur chemin le moins coûteux vers ce root.

### 2.3 Coût des liens et rôles des ports

| Débit du lien | Coût (méthode IEEE courte) |
|---|---|
| 10 Mb/s | 100 |
| 100 Mb/s (FastEthernet) | 19 |
| 1 Gb/s (GigabitEthernet) | 4 |
| 10 Gb/s | 2 |

| Rôle | Règle | État |
|---|---|---|
| Root Port | Un port par commutateur non-root : le meilleur chemin (coût cumulé le plus bas) vers le root | Forwarding |
| Designated Port | Un port par segment : celui du commutateur qui offre le meilleur chemin vers le root (égalité : BID de l'émetteur le plus bas, puis Port ID) | Forwarding |
| Alternate / Blocked | Port ni root ni désigné : il reste bloqué pour casser la boucle | Blocking |

### 2.4 États et temporisations (802.1D)
Un port passe par *Blocking → Listening → Learning → Forwarding*. Les temporisations par défaut sont : **Hello 2 s**, **Max Age 20 s**, **Forward Delay 15 s**. Après une panne, la convergence prend donc environ **30 à 50 secondes**.

### 2.5 Évolutions : RSTP, PortFast, BPDU Guard
- **RSTP (802.1w)** : négociation rapide entre switchs point-à-point, convergence en quelques secondes ;
- **PortFast** : passe directement en forwarding sur un port d'accès (PC, routeur). À n'utiliser *jamais* sur un port vers un autre switch ;
- **BPDU Guard** : coupe le port (err-disabled) si un BPDU arrive sur un port PortFast, ce qui protège contre un switch non autorisé.

## 3. Topologie de référence

La figure ci-dessous est la topologie de travail. Les numéros L1 à L7 désignent les liens, utilisés dans la suite du TP.

![Figure 1 — Topologie de référence](https://hackmd.io/_uploads/S1h3kkmJQg.png)

*Figure 1 — Topologie : R1/R2 sur ESW1, R3/R4 sur ESW2, triangle ESW1–ESW2–ESW3. Adresses de gestion en VLAN 1.*

(Une capture GNS3 « Switching Spanning Tree » montre la même topologie : https://hackmd.io/_uploads/By1O2CMkXg.png)

## 4. Plan d'adressage et câblage

### 4.1 Liens

| Lien | Équipement A | Interface | Équipement B | Interface | Type | Rôle |
|---|---|---|---|---|---|---|
| L1 | R1 | g0/0 | ESW1 | f1/0 | Accès | Hôte |
| L2 | R2 | g0/0 | ESW1 | f1/1 | Accès | Hôte |
| L3 | ESW1 | f1/2 | ESW2 | f1/2 | Trunk | Inter-switch |
| L4 | ESW1 | f1/3 | ESW3 | f1/0 | Trunk | Inter-switch (boucle) |
| L5 | ESW2 | f1/3 | ESW3 | f1/1 | Trunk | Inter-switch (boucle) |
| L6 | R3 | g0/0 | ESW2 | f1/0 | Accès | Hôte |
| L7 | R4 | g0/0 | ESW2 | f1/1 | Accès | Hôte |

### 4.2 Adressage (VLAN 1)

| Équipement | Interface | Adresse IP | Masque |
|---|---|---|---|
| R1 | g0/0 | 192.168.1.1 | 255.255.255.0 |
| R2 | g0/0 | 192.168.1.2 | 255.255.255.0 |
| R3 | g0/0 | 192.168.1.3 | 255.255.255.0 |
| R4 | g0/0 | 192.168.1.4 | 255.255.255.0 |
| ESW1 | Vlan1 | 192.168.1.11 | 255.255.255.0 |
| ESW2 | Vlan1 | 192.168.1.12 | 255.255.255.0 |
| ESW3 | Vlan1 | 192.168.1.13 | 255.255.255.0 |

## 5. Déroulement du TP

> **Règle de travail :** pour chaque phase, *prédisez* le résultat par écrit avant de taper les commandes, puis comparez. Un écart entre prédiction et observation est la meilleure occasion d'apprendre.

### Phase 0 — Préparation
Câblez la topologie, démarrez les équipements, puis configurez les noms, les adresses de gestion et les modes de ports.

```
! Sur ESW1 (idem ESW2 et ESW3 avec leur nom et IP)
enable
configure terminal
hostname ESW1
interface vlan 1
 ip address 192.168.1.11 255.255.255.0
 no shutdown
exit
interface range f1/0 - 1
 switchport mode access
 no shutdown
exit
interface range f1/2 - 3
 switchport mode trunk
 no shutdown
exit
end
write memory
```

```
! Sur chaque routeur (exemple R1)
interface g0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
end
```

> **Vérification :** `ping 192.168.1.x` doit répondre entre tous les hôtes. Si ce n'est pas le cas, reprenez la configuration avant de continuer.

### Phase 1 — Observer le STP par défaut
Ne configurez rien de plus. Relevez l'état du protocole sur chaque commutateur.

```
ESW1# show spanning-tree vlan 1
ESW1# show spanning-tree root
ESW1# show spanning-tree summary
```

**Questions :**
1. Quel est le *Root ID* annoncé ? Quelle est sa priorité (32768 par défaut) ?
2. Pourquoi est-ce le même Root ID sur les trois commutateurs ?
3. Quel commutateur est *root* ? Comparez les adresses MAC de base (`show version` ou `show interfaces vlan 1`) pour l'expliquer.
4. Combien de ports sont en état *Blocking* sur l'ensemble de la topologie ? Lesquels ?

### Phase 2 — Prédire puis forcer le pont racine
**Prédiction :** si l'on donne la priorité la plus basse à ESW1, quel commutateur deviendra root ? Quel sera le second, en cas de panne du premier ?

```
ESW1(config)# spanning-tree vlan 1 priority 4096
ESW2(config)# spanning-tree vlan 1 priority 8192
ESW1# show spanning-tree vlan 1 detail
```

Vérifiez que *This bridge is the root* apparaît sur ESW1. Sur ESW2, le root doit être ESW1, et ESW3 doit également le désigner.

### Phase 3 — Analyser les rôles des ports
Sur chaque commutateur, relevez le rôle et l'état de chaque port.

```
ESW2# show spanning-tree vlan 1
ESW3# show spanning-tree vlan 1
ESW3# show spanning-tree interface f1/0 detail
ESW3# show spanning-tree interface f1/1 detail
```

**Question clé :** le segment ESW2–ESW3 (L5) n'a qu'un seul port actif. Lequel est bloqué et pourquoi ? Expliquez à partir du coût cumulé vers le root (ESW3 atteint ESW1 directement par L4, ou via ESW2 par L5 puis L3) et, en cas d'égalité, du BID de l'émetteur.

> **Lecture attendue :** ESW1 n'a que des ports *Designated*. ESW2 a un port *Root* (f1/2) et des ports *Designated* (f1/0, f1/1, f1/3). ESW3 a un port *Root* (f1/0) et un port *Alternate* bloqué (f1/1).

### Phase 4 — Influencer le choix avec le coût de port
Le lien L4 est jugé plus cher que nécessaire pour atteindre le root. Simulez un lien plus lent et observez le changement de chemin.

```
ESW3(config)# interface f1/0
ESW3(config-if)# spanning-tree cost 200
ESW3# show spanning-tree vlan 1
```

**Question :** quel est le nouveau port root de ESW3 ? Pourquoi le port bloqué a-t-il changé ? Remettez le coût par défaut (`no spanning-tree cost`) avant la phase suivante.

### Phase 5 — Sécuriser les ports d'accès
Les ports vers R1, R2, R3 et R4 ne mènent jamais à un autre commutateur. Ils peuvent donc passer immédiatement en forwarding.

```
ESW1(config)# interface range f1/0 - 1
ESW1(config-if-range)# spanning-tree portfast
ESW1(config-if-range)# spanning-tree bpduguard enable
ESW2(config)# interface range f1/0 - 1
ESW2(config-if-range)# spanning-tree portfast
ESW2(config-if-range)# spanning-tree bpduguard enable
```

**Test :** sur ESW1, sans rien brancher, puis en branchant un petit commutateur sur f1/1 (à titre de démonstration uniquement), observez l'effet de BPDU Guard avec `show interfaces status err-disabled`.

### Phase 6 — Panne d'un lien et temps de convergence
Lancez un ping continu de R1 vers R3 (`ping 192.168.1.3 repeat 1000`), puis coupez le lien L3.

```
ESW1(config)# interface f1/2
ESW1(config-if)# shutdown
ESW1# show spanning-tree vlan 1
ESW2# show spanning-tree vlan 1
```

**Questions :**
1. Quel port de ESW2 change de rôle ? Quel port de ESW3 passe de *Alternate* à *Designated* ?
2. Mesurez le temps de coupure du ping. Comparez avec la théorie (30 à 50 s en 802.1D).
3. Remettez le lien (`no shutdown`) et observez la reconvergence.

### Phase 7 — Panne du pont racine
**Prédiction :** si ESW1 tombe, quel commutateur devient root ? Quel port de ESW3 devient root port ?

```
ESW1(config)# interface range f1/0 - 3
ESW1(config-if-range)# shutdown
ESW2# show spanning-tree vlan 1
ESW3# show spanning-tree vlan 1
```

Vérifiez la prédiction. Relevez le temps de coupure et notez que R1 et R2 sont désormais isolés du réseau : pourquoi ?

### Phase 8 — Comparer STP et RSTP

```
ESW1(config)# interface range f1/0 - 3
ESW1(config-if-range)# no shutdown
ESW1(config-if-range)# exit
ESW1(config)# spanning-tree mode rapid-pvst
ESW2(config)# spanning-tree mode rapid-pvst
ESW3(config)# spanning-tree mode rapid-pvst
ESW1# show spanning-tree summary
```

Refaites la panne de la phase 6 avec RSTP. Mesurez le temps de coupure et comparez avec le résultat du 802.1D. Expliquez la différence (négociation proposal / agreement, ports point-à-point).

## 6. Questions de réflexion
1. Pourquoi un commutateur ne peut-il pas simplement supprimer les trames en boucle grâce à un TTL comme le fait un routeur avec IP ?
2. Dans quel cas le BID est-il départagé par l'adresse MAC ? Pourquoi est-ce un problème en production, et comment l'éviter ?
3. Expliquez pourquoi PortFast ne doit jamais être activé sur un port menant à un autre commutateur.
4. Un administrateur baisse la priorité de ESW3 à 0. Que se passe-t-il et quelle est la conséquence pour la topologie ?
5. Quelle est la différence entre un port *Blocking* en 802.1D et un port *Discarding* en RSTP ?

## 7. Grille d'évaluation (sur 20)

| Critère | Points |
|---|---|
| Configuration de base (noms, IP, modes de ports) et connectivité validée | 3 |
| Identification correcte du root et explication du BID (phases 1 et 2) | 4 |
| Rôles des ports et port bloqué justifiés (phase 3) | 4 |
| Influence du coût et de la priorité (phases 2 et 4) | 2 |
| PortFast et BPDU Guard correctement appliqués (phase 5) | 2 |
| Analyse de la panne et mesure de la convergence (phases 6 à 8) | 3 |
| Réponses aux questions de réflexion et rédaction du compte rendu | 2 |

