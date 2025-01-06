# Mettre à jour le système :
 ```
lou@client3:~$ sudo apt update && sudo apt upgrade -y
 ```

# Installez Java avec une bonne version : 
 ```
lou@client3:~/minecraft-server$ sudo apt install openjdk-21-jdk*
 ```

# On vérifie que la version souhaitée est bien intallée :
 ```
 lou@client3:~/minecraft-server$ java -version
openjdk version "21.0.5" 2024-10-15
OpenJDK Runtime Environment (build 21.0.5+11-Ubuntu-1ubuntu124.04)
OpenJDK 64-Bit Server VM (build 21.0.5+11-Ubuntu-1ubuntu124.04, mixed mode, sharing)
 ```
    
    
# Créer un dossier pour le serveur :
 ```
lou@client3:~$ mkdir ~/minecraft-server && cd ~/minecraft-server
 ```
# + Télécharger le server Minecraft avec CurseForge (https://files.minecraftforge.net/net/minecraftforge/forge/index_1.20.1.html) et placer le fichier server.jar dans le dossier minecraft-server créé précédemment

# Lancer le ficher installer.jar pour avoir tous les fichiers utiles au server 
```
> lou@client3:~/forge-server$ java -jar forge-1.20.1-47.3.0-installer.jar --installServerl
```

# Modifier le fichier eula.txt 
```
lou@client3:~/forge-server$ sudo nano eula.txt

...
eula=true
...
```

# On lance le serveur, on écrit "/stop" pour l'arrêter 
 ```
 lou@client3:~/forge-server$ ./run.sh
 ```

 On peut utiliser & à la fin de la commande de lancement pour pouvoir mettre tourner le server en fond.

## On installe un mod (https://www.curseforge.com/minecraft/mc-mods/jei/download/5846810) et on le place dans le dossier du server


 # Automatiser le démarrage, Pour que le serveur se lance automatiquement au démarrage de la VM,
  
# On crée un service systemd

``` 
lou@client3:~/forge-server$ sudo nano /etc/systemd/system/minecraft.service
[sudo] password for lou: 
```

# On ajoute ce contenu pour Minecraft Java Edition :
```
[Unit]
Description=Minecraft Server
After=network.target

[Service]
User=lou
Group=lou
WorkingDirectory=/home/lou/forge-server
ExecStart=/bin/bash /home/lou/forge-server/run.sh
Restart=on-failure
LimitNOFILE=8192

[Install]
WantedBy=multi-user.target
```

# Activer et démarrer le service :

```
lou@client3:~/forge-server$ sudo systemctl daemon-reload
lou@client3:~/forge-server$ sudo systemctl start minecraft.service
lou@client3:~/forge-server$ sudo systemctl status minecraft.service
● minecraft.service - Minecraft Server
     Loaded: loaded (/etc/systemd/system/minecraft.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-12-02 12:32:48 CET; 7s ago
   Main PID: 4979 (bash)
      Tasks: 24 (limit: 4500)
     Memory: 419.9M (peak: 420.0M)
        CPU: 15.397s
     CGroup: /system.slice/minecraft.service
             ├─4979 /bin/bash /home/lou/forge-server/run.sh
             └─4980 java @user_jvm_args.txt @libraries/net/minecraftforge/forge/1.20.1-47.3.0/unix_args.txt

Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/INFO] [cp.mo.mo.Launcher/MODLAUNCHER]: ModLauncher running: args [>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/INFO] [cp.mo.mo.Launcher/MODLAUNCHER]: ModLauncher 10.0.9+10.0.9+m>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/INFO] [ne.mi.fm.lo.ImmediateWindowHandler/]: ImmediateWindowProvid>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/INFO] [mixin/]: SpongePowered MIXIN Subsystem Version=0.8.5 Source>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/WARN] [ne.mi.fm.lo.mo.ModFileParser/LOADING]: Mod file /home/lou/f>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/WARN] [ne.mi.fm.lo.mo.ModFileParser/LOADING]: Mod file /home/lou/f>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/WARN] [ne.mi.fm.lo.mo.ModFileParser/LOADING]: Mod file /home/lou/f>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/WARN] [ne.mi.fm.lo.mo.ModFileParser/LOADING]: Mod file /home/lou/f>
Dec 02 12:32:49 client3 bash[4980]: [12:32:49] [main/INFO] [ne.mi.fm.lo.mo.JarInJarDependencyLocator/]: No dependencies>
Dec 02 12:32:50 client3 bash[4980]: [12:32:50] [main/INFO] [cp.mo.mo.LaunchServiceHandler/MODLAUNCHER]: Launching targe>
lines 1-21/21 (END)

```

# Si on souhaite relancer le service 
```
ps aux | grep minecraft
sudo kill <PID>
```

# Commandes simples pour gérer le serveur 
```
sudo systemctl start minecraft.service   # Démarrer
sudo systemctl stop minecraft.service    # Arrêter
sudo systemctl restart minecraft.service # Redémarrer
sudo systemctl status minecraft.service  # Vérifier l'état
```

# On peut consulter les logs du serveur via journalctl :
```
sudo journalctl -u minecraft.service -f
```



# Effectuer une backup du server toutes les 24h 
```
lou@client3:~/forge-server$ nano backup_server.sh 

#!/bin/bash

BACKUP_DIR="/home/lou/backups"   # Répertoire où les sauvegardes seront stockées
SERVER_DIR="/home/lou/forge-server"  # Répertoire du serveur Minecraft
DATE=$(date +"%Y-%m-%d_%H-%M-%S")  # Utilisation de la date pour un nom unique de sauvegarde

# Créer le répertoire de sauvegarde si nécessaire
mkdir -p "$BACKUP_DIR"

# Supprimer la sauvegarde précédente s'il y en a une
if [ -f "$BACKUP_DIR/backup.tar.gz" ]; then
    echo "Suppression de la sauvegarde précédente..."
    rm "$BACKUP_DIR/backup.tar.gz"
else
    echo "Aucune sauvegarde précédente trouvée. Création d'une nouvelle sauvegarde."
fi

# Créer une nouvelle sauvegarde
echo "Création de la nouvelle sauvegarde..."
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" -C "$SERVER_DIR" .

# Vérification de l'exécution correcte de la commande tar
if [ $? -eq 0 ]; then
    echo "La sauvegarde a bien été effectuée avec succès !"
    echo "Sauvegarde terminée à $(date)" >> "$BACKUP_DIR/sauvegarde.log"  # Ajouter l'heure de la sauvegarde dans un fi>
else
    echo "Erreur lors de la création de la sauvegarde."
fi
```

# On rend le script exécutable 
```
chmod +x /home/lou/forge-server/backup.sh
```

# On automatise les backups avec cron
```
crontab -e

0 3 * * * /chemin/vers/ton/script.sh
```

# Pour tester le script
```
/home/lou/forge-server/backup.sh
```

# Créer un fichier qui enregistre les personnes qui se sont connectées au serveur
# Créer le script
```
lou@client3:~$ nano connexions_joueurs.sh


#!/bin/bash

# Chemin vers le fichier de logs du serveur Minecraft
LOG_FILE="/home/lou/forge-server/logs/latest.log"

# Fichier où on stockera les connexions des joueurs
CONNECTION_LOG="/home/lou/connexions_joueurs.log"

# Vérifier si le fichier de logs existe
if [ ! -f "$LOG_FILE" ]; then
    echo "Fichier de log introuvable : $LOG_FILE"
    exit 1
fi

# Suivre les logs en direct et capturer les connexions
tail -Fn0 "$LOG_FILE" | while read LINE; do
    # Si une ligne contient "joined the game", on l'ajoute au fichier
    if echo "$LINE" | grep -q "joined the game"; then
        echo "$LINE" >> "$CONNECTION_LOG"
    fi
done

```

# Rendre exécutable le script
```
lou@client3:~$ chmod +x /home/lou/connexions_joueurs.sh
```

# Tester le script 
```
lou@client3:~$ ./connexions_joueurs.sh
```

# Automatiser le script lorsque le server se lance
# Intègrer l’exécution du script dans le service minecraft.service de systemd
```
lou@client3:~$ sudo nano /etc/systemd/system/minecraft.service
[sudo] password for lou: 


[Unit]
Description=Minecraft Server
After=network.target

[Service]
User=lou
Group=lou
WorkingDirectory=/home/lou/forge-server
ExecStart=/bin/bash /home/lou/forge-server/run.sh
ExecStartPost=/home/lou/connexions_joueurs.sh
Restart=on-failure
LimitNOFILE=8192

[Install]
WantedBy=multi-user.target
```
# Recharger systemd et redémarre le service Minecraft
```
lou@client3:~$ sudo systemctl daemon-reload
lou@client3:~$ sudo systemctl restart minecraft.service
```