



----------------------------------------------------------------------------------------------------
----------------------------------

- mkdir /blob


- cp /run/media/roo/centos546-4645/AppStream /blob
(ou /mnt/)


- nano Baseos.repo


- [BaseOS]

name=BaseOs

baseurl=file:///blob/BaseOS/

enabled=1

gpgcheck=0


- pareil pour [AppStream]


- yum clean all

- yum install nano (test)

---------------------------------

systemctl set-default graphical.target

------------------------------------------------

tuned-adm profile throughput-performance

--------------------------------------------

rendre le journal systemd persistant:

nano /etc/systemd/journald.conf
storage=persistant
systemctl restart systemd-journald

-------

Ajouter un nouveau référentiel de packages à DNF : 
dnf config-manager --add-repo="file:///repo/BaseOS"

Voir les repolist : 
dnf repolist 

----------------------------------------------------------------------------------------------------
dans les fichiers cachés de /root,
nano .bash_profile (red hat),  .bash_rc pour (deb)? 

alias f='/home/user/scripts/t.sh'
chargé les nouvelles configurations en tapant source ~/.bash_profile
ajouter le chemin d'accès complet de votre script dans la définition de l'alias

------------------
----------------------------------------------------------------------------------------------------

systemctl isolate runlevel6.target : Modifie immediatement le runlevel et pour la session en cours

systemctl set-default runlevel6.target : Modifie le niveau de démarrage par défaut du système (actif au prochain redemmarage)

---------------------------------------------
----------------------------------------------------------------------------------------------------
RED HAT


Trouvez les fichiers appartenant à Tom et copiez-les dans le catalogue : /opt/dir 
find / -user Alice -type f -exec cp {} /opt/dir \;
----

Create une partition pour taille donnée :
#fdisk /dev/sdb
#n
#+2G
#w
#partprobe
#free -h
#mkswap /dev/sdb5
#swapon /dev/sdb5
#free -h
#vim /etc/fstab
and add below line : /dev/sdb5 swap swap defaults 0 0

----

/etc/login.defs (parametres des users)
Créer un user Tom avec un Id de 1111 et modifier son mdp
useradd -u 1111 -p password -m(home directory) tom 
usermod -aG profs tom 
groupadd
groupmod
----

Installez un serveur FTP et demandez un téléchargement anonyme à partir du catalogue /var/ftp/pub :
# cd /etc/yum.repos.d
# vim local.repo
[local]
name=local.repo
baseurl=file:///mnt
enabled=1
gpgcheck=0
# yum makecache
# yum install -y vsftpd
# service vsftpd restart
# chkconfig vsftpd on
# chkconfig --list vsftpd
# vim /etc/vsftpd/vsftpd.conf
anonymous_enable=YES

----

Configurer un serveur HTTP :
# wget http://station.domain40.example.com.
# dnf install -y httpd
# systemctl enable httpd
# cd /var/www/html
# wget http://test/test/example.html
# mv example.html index.html
# systemctl start httpd

----

Changez la capacité du volume logique nommé lvtest de 10M à 30M. et la taille de la plage flottante doit être comprise entre 25 et 35 :
# e2fsck -f /dev/vg1/lvm02
# resize2fs -f /dev/vg1/lvm02
# mount /dev/vg1/lvm01 /mnt
# lvreduce -L 1G -n /dev/vg1/lvm02
# lvdisplay (Verify)

----

Créez un groupe de volumes et définissez 16M comme extension. Et divisé un groupe de volumes contenant 50 extensions sur le groupe de volumes lv, faites-en un système de fichiers ext4 et monté automatiquement sous /mnt/data
En supposant que les partitions /dev/sda1 et /dev/sda2 existent déjà :
# pvcreate /dev/sda1 /dev/sda2
# vgcreate -s 16M vg01 /dev/sda1 /dev/sda2
# lvcreate -l 50 -n lv01 vg01
# mkfs.ext4 /dev/vg01/lv01
# mkdir -p /mnt/data
# blkid /dev/vg01/lv01
copy the UUID of /dev/vg01/lv01 in the output of that command.
# vim /etc/fstab
UUID="paste the UUID of /dev/vg01/lv01 here" /mnt/data ext4 defaults 0 0
# mount -a

----------------------------------------------------------------------------------------------------
Use nmtui !
Nouveau red hat = nmcli (nmcli device status) ; configuration ==> /etc/NetworkManager/system-connections/ens33.nmconnection

-----

Sur CentOs 7 = /etc/sysconfig/network-scripts/ifcfg-eth0
BOOTPROTO="static"
Ajouter les lignes suivantes :
IPADDR=192.168.200.5
NETMASK=255.255.0.0
GATEWAY=192.168.10.1
DNS1=192.168.40.1
DNS2=192.168.40.2
DOMAIN=
-
systemctl restart network.service

-----
Debian 11 = /etc/network/interfaces
allow-hotplug ens192
iface ens192 inet static
  address 192.168.100.14/24
  gateway 192.168.100.1
  dns-nameservers 192.168.100.11 1.1.1.1
  dns-domain test.local
  


------------------------------------------------Crontab----------------------------------------------

Planifier l’exécution d’un programme, script sur Linux:
modifier le fichier crontab : /etc/crontab
crontab -l : lister les cron en cours
minute hour day(1-31) month day(1-7) user command          |||||     *	any value ---- - ,	value list separator -----   -	range of values -----   /	step values   






---------------------------------------------ACL-----------------------------------------------------


setfacl -m : ajoute ou modifie des règles ACL pour un fichier ou un répertoire.

setfacl -x : supprime une règle ACL spécifique pour un fichier ou un répertoire.

setfacl -b : supprime toutes les règles ACL pour un fichier ou un répertoire.

getfattr : affiche les attributs étendus pour un fichier ou un répertoire.

setfattr : définit les attributs étendus pour un fichier ou un répertoire.

--

setfacl: permet de définir des ACL pour un fichier ou un répertoire.
Exemple : setfacl -m u:jane:rwx myfile (ajoute les droits de lecture, écriture et exécution pour l'utilisateur "jane" sur le fichier "myfile").

getfacl: affiche les ACL pour un fichier ou un répertoire.
Exemple : getfacl myfile (affiche les ACL pour le fichier "myfile").

setfacl -d: définit les ACL par défaut pour un répertoire.
Exemple : setfacl -d -m u:jane:rwx mydir (ajoute les droits de lecture, écriture et exécution pour l'utilisateur "jane" sur tous les fichiers créés dans le répertoire "mydir").

getfacl -d: affiche les ACL par défaut pour un répertoire.
Exemple : getfacl -d mydir (affiche les ACL par défaut pour le répertoire "mydir").

setfacl -x: supprime une ACL pour un fichier ou un répertoire.
Exemple : setfacl -x u:jane myfile (supprime les droits de l'utilisateur "jane" sur le fichier "myfile").

setfacl -b: supprime toutes les ACL pour un fichier ou un répertoire.
Exemple : setfacl -b myfile (supprime toutes les ACL pour le fichier "myfile").

setfacl -R: applique les ACL récursivement à tous les fichiers et répertoires dans un répertoire.
Exemple : setfacl -R -m u:jane:rwx mydir (ajoute les droits de lecture, écriture et exécution pour l'utilisateur "jane" sur tous les fichiers et répertoires dans le répertoire "mydir").

getfacl -R: affiche les ACL récursivement pour tous les fichiers et répertoires dans un répertoire.
Exemple : getfacl -R mydir (affiche les ACL récursivement pour tous les fichiers et répertoires dans le répertoire "mydir").


-------------------------------------------------------------------------------------------
--------------------------------=====SECURITE ET RESEAUX=====------------------------------
-------------------------------------------------------------------------------------------


---------------------------------------------netstat---------------------------------

netstat -p -v -n -a
ss -tua
-------------------------------------------nmcli--------------------------------------------


nmcli general status : Affiche le statut global de NetworkManager et des connexions réseau.

nmcli device status : Affiche le statut des périphériques réseau et des connexions actives.

nmcli connection show : Affiche les détails des connexions réseau configurées.

nmcli device wifi list : Affiche les réseaux Wi-Fi disponibles.

nmcli device wifi connect SSID password PASSWORD : Connecte à un réseau Wi-Fi spécifique avec le SSID et le mot de passe fournis.

nmcli connection add type ethernet ifname eth0 : Ajoute une connexion Ethernet avec le nom de périphérique spécifié.

nmcli connection modify eth0 ipv4.method manual ipv4.addresses 192.168.1.2/24 ipv4.gateway 192.168.1.1 : Configure une adresse IP statique pour la coEthernet spécifiée.

nmcli connection up eth0 : Active la connexion Ethernet spécifiée.

nmcli connection down eth0 : Désactive la connexion Ethernet spécifiée.

nmcli radio wifi off : Désactive le Wi-Fi sur l'ordinateur.

nmcli device show: Affiche les informations détaillées sur tous les périphériques réseau.

nmcli device set: Modifie les propriétés d'un périphérique réseau spécifique.

nmcli connection add: Ajoute une nouvelle connexion réseau.

nmcli connection modify: Modifie les propriétés d'une connexion réseau spécifique.

nmcli connection delete: Supprime une connexion réseau spécifique.


-------------------------------------------SE Linux---------------------------------------------

Barriere supplementaire de sécurité.

troubleshooting => setenforce permissive
ou setenforce 1 ou 0
troubleshooting => journalctl | grep sealert

sestatus : Affiche l'état actuel de SELinux.

nano /etc/sysconfig/selinux : pour modifier la ligne SELINUX en Permissive, Enforcing ou Disabled.

getsebool -a : Signale où un booléen SELinux particulier ou tous les booléens SELinux sont activés ou désactivés.

setsebool exemple_httpd_use_gpg on

semanage boolean -l : Plus de détails sur les booleans.

--

getenforce : Affiche le mode d'exécution actuel de SELinux (Enforcing, Permissive ou Disabled).

setenforce : Permet de changer le mode d'exécution de SELinux (Enforcing, Permissive ou Disabled).

semanage : Utilitaire pour gérer les règles SELinux, telles que la gestion des ports, des utilisateurs, des contextes de fichiers, etc.

restorecon : Réinitialise les contextes de sécurité SELinux pour les fichiers et les répertoires spécifiés.

audit2allow : Utilitaire pour générer des règles SELinux à partir des journaux d'audit SELinux.

ls -Z : pour voir le contexte

chcon : Change les contextes de sécurité SELinux pour les fichiers et les répertoires spécifiés.

(si SElinux est désactivé et qu'il faut le réactiver, il faut créer un fichier ".autorelabel" a la racine et mettre selinux sur permissive ou enforcing puis redémarrer) <== a voir si toujours utile avec la dernière MAJ

Enforcing : c'est le mode par défaut. Dans ce mode, SELinux impose strictement les politiques de sécurité définies. Si une action n'est pas autorisée par la politique, elle sera bloquée et une alerte sera enregistrée dans les journaux de sécurité.

Permissive : dans ce mode, SELinux ne bloque pas les actions qui ne sont pas autorisées par la politique de sécurité, mais il enregistre toujours une alerte dans les journaux de sécurité. Cela permet aux administrateurs de comprendre quelles actions seraient bloquées s'ils passent en mode Enforcing.

Disabled : dans ce mode, SELinux est complètement désactivé et ne fournit aucune sécurité supplémentaire. Toutes les actions sont autorisées sans restriction.


----------------------------------------------Firewalld----------------------------------------------

firewall-cmd --state : Affiche l'état actuel du pare-feu.

firewall-cmd --list-all : Affiche toutes les règles actuellement configurées dans le pare-feu.

firewall-cmd --add-port=<numéro de port>/tcp : Ouvre un port TCP spécifique.

firewall-cmd --remove-port=<numéro de port>/tcp : Ferme un port TCP spécifique.

firewall-cmd --add-service=<nom du service> : Ouvre tous les ports associés à un service spécifique.

firewall-cmd --remove-service=<nom du service> : Ferme tous les ports associés à un service spécifique.

firewall-cmd --permanent --add-port=<numéro de port>/tcp : Ouvre un port TCP spécifique de manière permanente (après un redémarrage).

firewall-cmd --permanent --remove-port=<numéro de port>/tcp : Ferme un port TCP spécifique de manière permanente (après un redémarrage).

firewall-cmd --reload : Recharge les règles du pare-feu.
  
firewall-cmd --zone=block --add-source=172.25.0.0/16 --permanent : Refuse domain 172.25.0.0/16 to access the server
  
firewall-cmd --zone=dmz --remove-service=ssh --permanent
  
firewall-cmd --zone=dmz --add-service=ssh

firewall-cmd --zone=<nom de la zone> --change-interface=<nom de l interface> : Associe une interface réseau à une zone spécifique.

firewall-cmd --get-zone : Affiche la zone actuelle

-------------------------------------------------UFW-------------------------------------------------

Commandes de base de UFW à utiliser :

sudo apt-get install ufw
sudo ufw allow ssh
ufw enable : Cette commande permet d'activer UFW et de commencer à filtrer le trafic de réseau.
ufw disable : Cette commande permet de désactiver UFW et de cesser de filtrer le trafic de réseau.

ufw default deny : Cette commande permet de refuser tout le trafic de réseau entrant par défaut, à moins qu'il ne soit explicitement autorisé par une règle de pare-feu.
ufw default allow : Cette commande permet d'autoriser tout le trafic de réseau entrant par défaut, à moins qu'il ne soit explicitement refusé par une règle de pare-feu.

ufw allow <port>/<protocole> : Cette commande permet d'autoriser le trafic entrant sur un port particulier et avec un protocole particulier. Par exemple, pour autoriser le trafic HTTP entrant sur le port 80, vous pouvez utiliser la commande suivante : ufw allow 80/tcp
ufw deny <port>/<protocole> : Cette commande permet de refuser le trafic entrant sur un port particulier et avec un protocole particulier. Par exemple, pour refuser le trafic FTP entrant sur le port 21, vous pouvez utiliser la commande suivante : ufw deny 21/tcp

sudo ufw allow from 10.10.10.0/24 to any port 2222 : Autoriser uniquement le sous-réseau 10.10.10.0/24 à se connecter sur le port 2222
sudo ufw allow from 10.10.10.1 to any port 2222 : Autoriser uniquement la machine 10.10.10.1 à se connecter sur le port 2222

-------------------------------------------------ClamAV-------------------------------------------------

ClamAV : ClamAV est un scanner de virus open source pour Linux. Il peut être utilisé pour détecter et éliminer les virus, les logiciels espions et d'autres logiciels malveillants de votre système.

On Debian: sudo apt-get update && sudo apt-get install clamav clamav-daemon
On RedHat: sudo dnf update && sudo dnf install clamav-daemon

Mettre à jour ClamAV sur Linux:
La base de données de signature de ClamAV est mise à jour automatiquement par le daemon freshclam.
sudo systemctl stop clamav-freshclam : Pour arrêter le FreshClam, suivez simplement la commande Terminal
sudo freshclam : Puis on utilise l’utilitaire freshclam pour se connecter, télécharger et installer les définitions virales
sudo systemctl start clamav-freshclam : Pour relancer le service

sudo clamscan --infected --recursive --remove /   : Analyser le systeme entier
Les options :
–infected : Imprime uniquement les fichiers infectés
–remove : Supprime les fichiers infectés automatiquement
–recursive : Tous les sous-répertoires de l’annuaire seront numérisés
-r -i et enfin –bell pour beeper à chaque détection
–move : Pour déplacer les fichiers détectés vers un dossier spécifique (quarantaine). EXEMPLE : clamscan -r --move=~/VIRUS /tmp/


-------------------------------------------------Fail2ban-------------------------------------------------

Fail2ban est un utilitaire de sécurité qui surveille les journaux du système et bloque les adresses IP suspectes qui tentent de se connecter de manière abusive.(evite les ddos). Complement : Détecter et bannir les adresses IP qui tentent de se connecter de manière répétée à un service (par exemple, SSH) en utilisant des mots de passe incorrects ou en faisant des tentatives de connexion infructueuses.

sudo fail2ban-client status : afficher la liste des jails de fail2ban avec Fail2ban avec l’option status
Status
|- Number of jail:      4
`- Jail list:   ban1day, nginx-limit-req, ssh-reflection, sshd

Recharger la configuration, utilisez la commande reload comme ceci :
sudo fail2ban-client reload
Pour relancer le service fail2ban :
sudo fail2ban-client restart

Bannir une adresse IP dans une prison (jail), on utilise la commande banip de cette manière :
fail2ban-client set <JAIL> banip <IP>
Par exemple pour bannir l’adresse IP 1.1.1.1 dans la jail ssh :
fail2ban-client set sshd banip 1.1.1.1

Débannir une adresse IP
fail2ban-client set <JAIL> unbanip <IP>
Ainsi, par exemple pour retirer l’adresse IP 1.1.1.1. de la prison sshd :
fail2ban-client set sshd unbanip 1.1.1.1 
Débannir toutes les adresses IP
sudo fail2ban-client unban --all

Lister les adresses IP bannies
fail2ban-client banned
Si vous désirez afficher que les adresses IP bannies sur un jail en particulier :
fail2ban-client get sshd banned


-------------------------------------------------AppArmor-------------------------------------------------

(Voir aussi SELinux, qui est similaire)
AppArmor est un système de contrôle d’accès obligatoire (Mandatory Access Control) qui s’appuie sur l’interface LSM (Linux Security Modules) fournie par le noyau Linux.
Il permet d’établir des règles et politiques d’accès aux processus, via des profiles, afin de confiner les accès systèmes.
De ce fait, cela interdit tout accès système non désirés.
AppArmor est donc un utilitaire important pour améliorer la sécurité de votre serveur Linux en protégeant contre les accès frauduleux via des vulnérabilités.

Installez AppArmor et ses outils avec APT :
apt install apparmor apparmor-utils apparmor-profiles apparmor-profiles-extra


mkdir -p /etc/default/grub.d  : Apparmor est un module de noyau Linux, vous devez l’activer dans grub avec ces commandes
Créez le fichier /etc/default/grub.d/apparmor.cfg avec le contenu suivant : GRUB_CMDLINE_LINUX_DEFAULT="$GRUB_CMDLINE_LINUX_DEFAULT apparmor=1 security=apparmor"
Enregistrer et sortir, puis exécuter :
update-grub
Redemarrer le serveur
systemctl status apparmor : Vérifier l’état du service AppArmor avec systemctl

Copier les profiles “extras”
Les paquets apparmor-profiles et apparmor-profiles-extra fournissent des profiles supplémentaires qui se trouvent dans /usr/share/apparmor/extra-profiles/.
Vous pouvez copier l’intégralité dans le répertoire de profil AppArmor /etc/apparmor.d/ ou ne copier que ceux que vous comptez utiliser pour une installation plus “propre”.
cp /usr/share/apparmor/extra-profiles/*/etc/apparmor.d/

Vérifier l’état des profiles AppArmor
AppArmor fonctionne avec des profiles pour chaque binaire qui établisse les politiques autorisées. Deux mode de fonctionnement par profile est possible :
Mode complain : les violations de la politique ne seront enregistrées dans les logs et aucun blocage n’est effectué. C’est un mode d’apprentissage pour régler le profile
Mode enforce : les opérations qui violent la politique seront bloquées
aa-status : Pour énumérer tous les profils Apparmor chargés pour les applications et les processus et détaille leur statut (loaded., complain mode, enforce mode)

Activer un profile en mode complain :
aa-complain </chemin/binaire> : On utilise la commande aa-complain pour passer un binaire en mode plainte.
aa-complain /usr/bin/ping : Par exemple pour passer la commande ping en mode complainte
Activer un profile en mode enforce :
aa-enforce </chemin/binaire>

Comme vu avant, Il faut avant tout configurer un profile pour limiter les applications qui exposent le système.
C’est à dire les applications réseaux avec des ports ouverts, celles qui exécutent des cron etc.
Pour vous y aider, Apparmor possède la commande aa-unconfined qui liste les applications non confinés et qu’il faut en priorité configurer:
aa-unconfined /usr/sbin/nginx

-------------------------------------------------CONTAINER PODMAN-------------------------------------------------

podman login registry.redhat.io

podman exec -it <nom_conteneur> </bin/bash>
  
podman run: Cette commande est utilisée pour exécuter un conteneur. Par exemple, pour exécuter un conteneur de la distribution Alpine Linux, vous pouvez utiliser la commande suivante :
podman run -it alpine /bin/sh
  
podman ps: Cette commande affiche une liste des conteneurs en cours d'exécution. Par exemple, pour afficher la liste des conteneurs en cours d'exécution sur votre système, vous pouvez utiliser la commande suivante :
podman ps
  
podman images: Cette commande affiche une liste des images de conteneurs disponibles sur votre système. Par exemple, pour afficher la liste des images disponibles, vous pouvez utiliser la commande suivante :
podman images
  
podman stop: Cette commande est utilisée pour arrêter un conteneur en cours d'exécution. Par exemple, pour arrêter un conteneur avec l'ID 123abc, vous pouvez utiliser la commande suivante :
podman stop 123abc
  
podman start: Cette commande est utilisée pour démarrer un conteneur arrêté. Par exemple, pour démarrer un conteneur avec l'ID 123abc, vous pouvez utiliser la commande suivante :
podman start 123abc
  
podman restart: Cette commande est utilisée pour redémarrer un conteneur en cours d'exécution. Par exemple, pour redémarrer un conteneur avec l'ID 123abc, vous pouvez utiliser la commande suivante :
podman restart 123abc
  
podman rm: Cette commande est utilisée pour supprimer un conteneur. Par exemple, pour supprimer un conteneur avec l'ID 123abc, vous pouvez utiliser la commande suivante 
podman rm 123abc
  
podman rmi: Cette commande est utilisée pour supprimer une image de conteneur. Par exemple, pour supprimer l'image my-image:latest, vous pouvez utiliser la commande suivante :
podman rmi my-image:latest
  
podman build: Cette commande est utilisée pour créer une image de conteneur à partir d'un fichier Dockerfile ou d'une archive de contexte de build. Par exemple, pour créer une image à partir d'un Dockerfile dans le répertoire courant, vous pouvez utiliser la commande suivante :
podman build -t my-image:latest .
  
podman commit: Cette commande est utilisée pour créer une nouvelle image de conteneur à partir d'un conteneur en cours d'exécution. Par exemple, pour créer une nouvelle image à partir d'un conteneur avec l'ID 123abc, vous pouvez utiliser la commande suivante :
podman commit 123abc my-new-image:latest
  
podman export: Cette commande est utilisée pour exporter le système de fichiers d'un conteneur dans une archive tarball. Par exemple, pour exporter le système de fichiers d'un conteneur avec l'ID 123abc dans un fichier my-container.tar, vous pouvez utiliser la commande suivante :
podman export 123abc > my-container.tar
