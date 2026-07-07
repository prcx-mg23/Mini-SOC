# Mini-SOC — Wazuh & Suricata - En cours

Lab de cybersécurité défensive et offensive construit sur infrastructure hybride (AWS EC2 + VMware), simulant un pipeline complet de détection d'intrusion : reconnaissance et exploitation offensive côté attaquant, capture réseau et corrélation SIEM côté défenseur.

## Objectif

Comprendre, des deux côtés du miroir, comment une attaque se déroule et comment elle peut être détectée :

- **Côté offensif** : reconnaissance, exploitation de vulnérabilités classiques (web, SMB, brute force, injection), post-exploitation
- **Côté défensif** : capture réseau avec Suricata (IDS), remontée et corrélation des événements dans Wazuh (SIEM), écriture de règles de détection personnalisées

Le laboratoire est documenté sous forme de TP progressifs (TP1 à TP15), chacun suivant une structure fixe : objectif, principe théorique, cible, procédure, analyse des résultats, détection Wazuh, points clés.

## Architecture

| Composant | Rôle | Détails |
|---|---|---|
| **AWS EC2 (Ubuntu 22.04)** | Wazuh Manager + Dashboard | 8 Go RAM, ports 22/443/1514/1515 |
| **VM Ubuntu 22.04** | Agent Wazuh + Suricata IDS | Capture réseau en mode promiscuous (af-packet), ~51 000 règles Emerging Threats |
| **VM Kali Linux** | Machine d'attaque | Nmap, Metasploit, Hydra, Gobuster, Nikto, Burp Suite, Enum4linux |
| **VM Metasploitable 2** | Cible volontairement vulnérable | Ubuntu 8.04 — non supervisable (agent Wazuh incompatible), visible uniquement via Suricata |

Toutes les VMs partagent le même réseau NAT VMware (`192.168.X.0/24`), ce qui permet à Suricata de capturer le trafic inter-VM en mode promiscuous sans configuration de port mirroring supplémentaire.

## Pipeline de détection

1. Kali attaque Metasploitable 2 sur le réseau partagé
2. Suricata capture le trafic sur l'interface réseau de la VM Ubuntu et génère des alertes structurées (`eve.json`) à partir de son ruleset
3. L'agent Wazuh local lit ce fichier JSON en continu et transmet chaque événement au Wazuh Manager
4. Le Manager applique ses propres règles de décodage et de corrélation, visibles depuis le Dashboard (module Threat Hunting)

## Attaques et détection

| TP | Contenu |
|---|---|
| 1 | Reconnaissance réseau avec Nmap |
| 2 | Audit web avec Nikto |
| 3 | Énumération SMB |
| 4 | Brute force SSH/Telnet avec Hydra |
| 5 | SQL Injection  |
| 6 | Cross-Site Scripting (XSS) |
| 7 | Command Injection |
| 8 | Upload de web shell |
| 9 | Exploitation FTP  |
| 10 | Exploitation Samba |
| 11 | Exploitation UnrealIRCd |
| 12 | Exploitation Tomcat |
| 13 | Escalade de privilèges |
| 14 | Détection des attaques dans Wazuh |
| 15 | Création de règles de détection personnalisées |

## Compétences mises en pratique

- Déploiement et durcissement d'un SIEM (Wazuh Manager, OpenSearch, Dashboard)
- Configuration d'un IDS réseau (Suricata) en mode capture promiscuous, troubleshooting af-packet et intégration systemd
- Intégration de sources de logs hétérogènes (agents natifs, fichiers JSON, syslog) dans un pipeline de détection centralisé
- Reconnaissance et exploitation de vulnérabilités web et réseau classiques (OWASP Top 10, CVE historiques)
- Analyse d'alertes IDS/SIEM et réduction du bruit (tuning de règles, gestion des faux positifs)

## Stack technique

`Wazuh` · `Suricata` · `AWS EC2` · `VMware` · `Kali Linux` · `Metasploitable 2` · `Nmap` · `Nikto` · `Hydra` · `Metasploit`

---
