<!-- Source : https://github.com/Satom-IT-Learning-Solutions/Catalogue-Sujets-GIT/blob/main/1.Compétences/Réseau/PPE_Competences_Reseau_69_CCNA/Projets/PPE_Competences_Reseau_69_CCNA_P02_Debutant/PPE_Competences_Reseau_69_CCNA_P02_Debutant_Sujet.md — copie intégrale du 23.11.2026 (sujet catalogue, version PNETLab). L'énoncé GNS3 du TP est enonce.md. -->

---
id: PPE-C69-P02
titre: "Configuration de base d'un routeur et d'un switch"
niveau: Débutant
competence: PPE-C69 — Maîtriser les fondamentaux réseau Cisco (CCNA)
famille: Réseau
certification: CCNA 200-301
statut: pilote
version: 1.0
auteur: Morrad Belrhali
date_creation: 2026-10-14
duree_estimee: 2 h
bareme: 20 pts
environnement: [PNETLab, Cisco IOS]
---

# P02 — Configuration de base d'un routeur et d'un switch

## 1. Contexte
Le même cabinet installe un routeur et un switch neufs. La direction exige un nom d'équipement clair, des mots de passe protégés et un accès d'administration sécurisé.

Toutes les données sont fictives.

## 2. Objectifs
- Nommer les équipements et protéger le mode privilégié.
- Configurer les adresses IP de la topologie.
- Sécuriser les accès console et VTY.
- Vérifier et sauvegarder la configuration.

## 3. Environnement
Nœuds PNETLab : routeurs Cisco IOL (L3), commutateurs Cisco IOL (L2) et postes Linux ou Windows selon le besoin. Les noms d'interfaces suivent la convention IOL : `e0/x` pour Ethernet, `s0/x` pour série.

| Équipement | Rôle | Adresses et détails |
|---|---|---|
| R1 | Routeur (e0/0) | 10.10.1.1/24 |
| SW1 | Commutateur (SVI) | 10.10.1.2/24 (VLAN 1) |
| PC1 | Poste de test | 10.10.1.10/24 |

![Topologie PNETLab P02](Topologie_P02.svg)

## 4. Déroulement sur 5 jours

### Jour 1 — Lundi : déploiement de la maquette
- Démarrer la maquette PNETLab, vérifier chaque nœud et chaque liaison, puis écrire la procédure de déploiement.
- **Livrable du jour** : procédure de déploiement et capture de la maquette démarrée.

### Jours 2 à 4 — Mardi à jeudi : configuration et vérification
- Réaliser les tâches du sujet dans l'ordre, en vérifiant chaque étape avant de passer à la suivante.
- **Erreur fréquente** : configurer sans vérifier. Chaque tâche se termine par la commande de contrôle indiquée.

### Jour 5 — Vendredi : dépannage
- Scénario 1 (guidé) : une interface est en `administratively down` ; la réactiver.
- Scénario 2 : l'accès VTY refuse la session ; vérifier `transport input` et le mot de passe.
- **Livrables** : configurations finales, journal de diagnostic des scénarios, procédure suivie.

## 5. Travail demandé

### Tâche 1 — Nommer et protéger le mode privilégié (5 pts sur 20)
- `hostname R1` puis `hostname SW1`.
- `enable secret` avec un mot de passe robuste.
- `service password-encryption`.
- `banner motd #Acces autorise uniquement#`.

**Contrôle attendu :** Le nom apparaît dans le prompt et `show running-config` ne montre aucun mot de passe en clair.

### Tâche 2 — Adressage (5 pts sur 20)
- R1 : `interface e0/0`, `ip address 10.10.1.1 255.255.255.0`, `no shutdown`.
- SW1 : `interface vlan 1`, `ip address 10.10.1.2 255.255.255.0`, `no shutdown`.
- SW1 : `ip default-gateway 10.10.1.1`.

**Contrôle attendu :** `show ip interface brief` affiche « up/up » sur les interfaces utilisées.

### Tâche 3 — Accès console et VTY (5 pts sur 20)
- `line console 0` : `password`, `login`, `logging synchronous`.
- `line vty 0 4` : `password`, `login`, `exec-timeout 5 0`.

**Contrôle attendu :** Une session console demande le mot de passe.

### Tâche 4 — Vérification et sauvegarde (5 pts sur 20)
- Depuis PC1 : `ping 10.10.1.1` puis `ping 10.10.1.2`.
- `copy running-config startup-config`.

**Contrôle attendu :** Les deux pings réussissent et la configuration est sauvegardée.

## 6. Livrable
Dossier `PPE-C69_Nom_Prenom` avec les captures des tâches, les configurations de chaque équipement (`show running-config`), le tableau des vérifications et un compte rendu d'une page.

## 7. Critères d'évaluation

| Critère | Points |
|---|---|
| Tâche 1 : Nommer et protéger le mode privilégié | 5 |
| Tâche 2 : Adressage | 5 |
| Tâche 3 : Accès console et VTY | 5 |
| Tâche 4 : Vérification et sauvegarde | 5 |
| **Total** | **20** |

## 8. Aide
- Un mot de passe `secret` est chiffré ; `password` ne l'est qu'avec `service password-encryption`.
- Si le ping vers SW1 échoue, vérifier `ip default-gateway` et l'état de `vlan 1`.

## 9. Corrigé
> Réservé formateur : `Catalogue-Sujets_corriger-GIT/Compétences/Réseau/PPE_Competences_Reseau_69_CCNA/Projets/PPE_Competences_Reseau_69_CCNA_P02_Debutant/PPE_Competences_Reseau_69_CCNA_P02_Debutant_Corrige.md`
