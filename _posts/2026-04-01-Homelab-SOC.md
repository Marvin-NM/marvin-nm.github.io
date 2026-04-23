---
layout: post
title: "Homelab SOC — Architecture et objectifs"
date: 2026-02-19
categories: [Cybersecurity, Homelab, Blue Team]
tags: [SIEM, Wazuh, SOC, Detection, Active Directory]

---

## Pourquoi ce lab

Comprendre comment un attaquant opère, c'est bien.
Être capable de le détecter, c'est mieux.
Ce homelab est construit autour d'un objectif concret :
simuler des scénarios d'attaque réalistes et développer
ma capacité à les détecter, les analyser et y répondre
— comme le ferait un analyste SOC face à un vrai incident.
L'environnement repose sur un domaine Active Directory
(**`cador.inc`**), cible privilégiée des attaquants réels
et terrain d'entraînement idéal pour les techniques
d'attaque et de détection les plus courantes en entreprise.

---

## Architecture générale

[![Diagramme réseau](/assets/img/diagramme.svg)](/assets/img/diagramme.svg)

L'infrastructure est segmentée en quatre sous-réseaux
distincts — pas uniquement pour des raisons techniques,
mais parce que cette segmentation reflète ce qu'on
retrouve dans un SI d'entreprise réel et permet de
générer des flux réseau réalistes à analyser.

---

## Les briques et leur rôle défensif

### Supervision et détection — 10.0.1.0/24

**Wazuh (SIEM/XDR)** est le cœur du dispositif défensif.
Il collecte les événements de toutes les machines du lab,
corrèle les alertes et permet de construire des règles
de détection personnalisées.
C'est sur cette brique que je travaille la compétence
qui m'intéresse le plus : transformer des événements
bruts en alertes exploitables — ce que fait un analyste
SOC au quotidien.

### Environnement cible — 10.0.10.0/24

**Windows Server 2019** héberge le contrôleur de domaine

**cador.inc**, intentionnellement mal configuré pour
reproduire des chemins d'attaque réalistes (inspiré du
projet *Game of Active Directory Light*).

**Windows 10** est la workstation utilisateur — cible
typique des attaques de type phishing, mouvement latéral
ou élévation de privilèges.
L'objectif n'est pas d'avoir un AD sécurisé, mais un AD
qui ressemble à ce qu'on trouve en entreprise, avec les
mêmes erreurs de configuration que celles rencontrées
dans des incidents réels.

### Attaquant — 172.16.10.0/24

**Kali Linux** me permet de simuler les techniques
offensives les plus courantes : reconnaissance,
Kerberoasting, Pass-the-Hash, mouvement latéral.
L'intérêt n'est pas de "hacker" — c'est de générer
les artefacts et les traces que Wazuh doit détecter.
Chaque attaque lancée depuis Kali est une occasion
de vérifier ce que le SIEM voit, ce qu'il rate,
et pourquoi.

### Isolation et analyse — 10.0.99.0/24

**FlareVM** est isolé du reste du réseau pour l'analyse
de malware et le reverse engineering. Ce sous-réseau
ne communique pas avec les autres — pour éviter toute
propagation accidentelle lors de l'analyse de samples
réels.

---
## Les scénarios que je veux couvrir

Ce lab est construit pour travailler des scénarios
concrets, pas des exercices théoriques :
- **Détection d'une attaque AD** (Kerberoasting,
 Pass-the-Hash) — qu'est-ce que Wazuh voit exactement ?
- **Analyse d'un mouvement latéral** — comment le
 retracer dans les logs ?
- **Investigation post-incident** — collecte d'artefacts,
 reconstruction de la timeline
- **Analyse de malware** — comportement, IoC,
 règles de détection
Chaque scénario fera l'objet d'un writeup dédié.

---

## Évolutions prévues

Le lab continue d'évoluer vers des capacités SOC
plus avancées :

**Forensique et réponse à incident**
Ajout de **Tsurugi Linux** et **REMnux** pour couvrir
l'analyse post-mortem et l'investigation numérique —
les mêmes outils utilisés lors d'une vraie réponse
à incident.

**Pratique SIEM enterprise**
Déploiement de **Splunk** avec le dataset **Boss of
the SOC (BOTS v3)** — pour travailler sur des scénarios
d'investigation réalistes et me familiariser avec
l'outil le plus cité dans les offres SOC.

**QRadar**
Familiarisation avec un second SIEM enterprise,
présent dans de nombreuses organisations.

---

*Les writeups des scénarios d'attaque/détection
arrivent au fil de la progression du lab.*
