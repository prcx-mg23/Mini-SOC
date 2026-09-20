# Mini-SOC — Wazuh & Suricata

Lab de cybersécurité défensive et offensive construit sur infrastructure hybride (AWS EC2 + VMware), simulant un pipeline complet de détection d'intrusion : reconnaissance et exploitation offensive côté attaquant, capture réseau et corrélation SIEM côté défenseur.

## Objectif

Comprendre, des deux côtés du miroir, comment une attaque se déroule et comment elle peut être détectée :

- **Côté offensif** : reconnaissance, exploitation de vulnérabilités classiques (nmap, brute force)
- **Côté défensif** : capture réseau avec Suricata (IDS), remontée et corrélation des événements dans Wazuh (SIEM), écriture de règles de détection personnalisées


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


## Compétences mises en pratique

- Déploiement et durcissement d'un SIEM (Wazuh Manager, OpenSearch, Dashboard)
- Configuration d'un IDS réseau (Suricata) en mode capture promiscuous, troubleshooting af-packet et intégration systemd
- Intégration de sources de logs hétérogènes (agents natifs, fichiers JSON, syslog) dans un pipeline de détection centralisé
- Analyse d'alertes IDS/SIEM et réduction du bruit (tuning de règles, gestion des faux positifs)

## Stack technique

`Wazuh` · `Suricata` · `AWS EC2` · `VMware` · `Kali Linux` · `Metasploitable 2` · `Nmap` · `Nikto` · `Hydra` · `Metasploit`

---
