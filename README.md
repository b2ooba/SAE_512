# SAE_512

Ce fichier est à lire, explicatif du projet.

Ce projet vise à comprendre et mettre en œuvre les principes de l'agilité dans un contexte de projet informatique, en utilisant des outils de gestion de version (GitHub) et de gestion de projet (Jira). Le projet consiste à configurer un réseau informatique sous forme de code, en utilisant l'outil MARIONET, version .

Pour ce faire, voici les étapes suivies : 

-> Construire un double réseau de 6 machines chacun.
-> Configurer un système avec 2 services DHCP (gérés par une machine contenant le service DHCP).
-> Utiliser 3 switchs pour filtrer les communications entre les sous-réseaux.
-> Mettre en place un routeur (FW) pour jouer le rôle de pare-feu entre le routeur DHCP et la passerelle (Gateway).


Configuration du DHCP : 

- Nous rentrons dans le fichier de configuration avec la commande : 
nano /etc/default/isc-dhcp-server

- Puis nous ajoutons les lignes suivantes : 

INTERFACESv4="eth0 eth1 eth2"
option domain-name-servers 193.54.26.1; 
subnet 192.168.10.0 netmask 255.255.255.0 {
        range 192.168.10.1 192.168.10.10;
        option routers 192.168.10.254;
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.10.255;
        max-lease-time 3600;
}

subnet 192.168.20.0 netmask 255.255.255.0 { 
        range 192.168.20.1 192.168.20.10;
        option routers 192.168.20.254; 
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.20.255;
        max-lease-time 3600;
}
subnet 192.168.30.0 netmask 255.255.255.0 {
        range 192.168.30.1 192.168.30.10;
        option routers 192.168.30.254;
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.30.255;
        max-lease-time 3600;
}

- Après cela, nous redémarrons le service DHCP avec la commande suivante : 
/etc/init.d/dhcpd restart


- Demander un bail sur la machine client :
dhclient eth0

 - Activer le routage sur le serveur DHCP :
nano /etc/sysctl.conf
net.ipv4.ip_forward = 1

 - Configuration des VLANs sur les Switchs :
vlan/create 1
vlan/create 2
port/setvlan 2 1
port/setvlan 3 1
port/setvlan 4 2 
port/setvlan 5 2

#Mode Trunks : 
port/setvlan 1 1 
port/setvlan 1 2 

=> Nous créons les deux VLANs : 
1- Correspond aux machines avec des chiffres.
2- Correspond aux machines avec des lettres.

Puis nous affilions chaque port correspondant au VLAN souhaité.
Ainsi que le mode Trunk associé au port 1.

