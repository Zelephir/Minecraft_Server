# Projet de Serveur Minecraft sur Debian

- CAETANO Maël
- CABANES Hugo
- LEFEBVRE Lou
- ONER Yarkin

## Introduction

Ce projet consiste à mettre en place un serveur Minecraft moddé sur une machine Debian, accessible en ligne via une connexion sécurisée avec OpenVPN, et incluant un système de logs pour suivre les événements du serveur et faciliter sa gestion. Le serveur supportera des mods personnalisés et sera accessible par les joueurs via un tunnel VPN.

## Prérequis

Avant de commencer, il est nécessaire d'avoir :

    - Une machine Debian (version stable recommandée).
    - Un accès root ou un accès sudo pour installer des logiciels.
    - Une installation de Java (Minecraft requiert Java 17 ou plus).

Dépendances nécessaires

    - Java (version 17 ou supérieure, en fonction des mods)
    - Forge (outil de modding pour Minecraft)
    - Screen (pour exécuter le serveur Minecraft en tâche de fond)
    - wget (pour télécharger le fichier du serveur Minecraft moddé)
    - Logrotate (pour gérer les fichiers de log)
    - mcserverlog (pour analyser et gérer les logs du serveur)

# Étapes de configuration

### 1. Installation de Java

Installer la version appropriée de Java pour faire fonctionner le serveur Minecraft moddé. Minecraft moddé nécessite généralement Java 17 ou une autre version spécifique selon les mods utilisés.

### 2. Installation de Forge pour Minecraft

Télécharger et installer Forge, le gestionnaire de mods, pour la version de Minecraft choisie (1.20.1). Forge permet de charger et d'exécuter des mods sur le serveur Minecraft moddé.

### 3. Téléchargement et installation des mods

Télécharger les mods compatibles avec la version de Minecraft et de Forge utilisée. Placer les fichiers .jar des mods dans le répertoire mods du serveur Minecraft pour qu'ils soient chargés lors du démarrage du serveur.

### 4. Configuration du serveur Minecraft

Modifier le fichier de configuration server.properties pour ajuster les paramètres du serveur, tels que le mode de jeu, la difficulté, le nombre maximal de joueurs, et le port d'écoute du serveur.

### 5. Connexion au serveur via OpenVPN

Configurer et utiliser OpenVPN pour établir une connexion sécurisée entre les clients et la machine Debian. Avec OpenVPN, il n'est pas nécessaire d'ouvrir les ports de la box, et les joueurs pourront se connecter au serveur Minecraft via un tunnel VPN, en utilisant l'adresse IP privée assignée à la machine Debian.

### 6. Mise en place d'un système de logs

Installer et configurer un système de logs pour suivre les événements du serveur Minecraft. Utiliser des outils comme Logrotate pour gérer la rotation des logs et éviter qu'ils ne deviennent trop volumineux. Configurer également des outils comme mcserverlog pour analyser et consulter les logs de manière détaillée.

### 7. Test de la connexion en ligne

Vérifier que le serveur Minecraft est accessible en ligne via le tunnel VPN, en utilisant l'adresse IP interne de la machine Debian. Tester l'accès depuis un autre appareil pour s'assurer que tout fonctionne correctement.