# Exercice

Vlan10 "tech"
Vlan20 "Admin"
Vlan99 "Management"


## Batiment 1


### Swi@ch S1-bat1

Les ports : 
- 1-5 : vlan 10 (tech)
- 6-10 : vlan 20 (Admin)
- 11-24 : vlan 1 (Defaut)

Plage d'adressage : 192.168.vlan.x

#### Les PCs

On note que PC0 est sur le port n°13, dans le Vlan1. Je le modifie pour l'integrer dans le vlan10. rebranchement sur le port 0/3

Afin de valider la configuration des ordinateurs derrières le switchs, j'ai configuré des adresses ip fixes  

- PC 09 : 192.168.10.10 
- PC 00 : 192.168.10.20
- PC 10 : 192.168.10.30

Ce paramétrage est temporaire, le temps que le serveur dhcp fonctionne.

J'ai utilisé ping pour m'assurer que les 3 pc communiquent bien.

#### Laptop de management

L'ordinateur laptop est l'ordinateur d'un administrateur réseaux ayant vocation à utilisé le vlan 99

On décide de mettre une ip fixe car c'est un réseau de management. "192.168.99.10"


### Swi@ch S2-bat1

Les ports : 
- 1-5 : vlan 10 (tech)
- 6-10 : vlan 20 (Admin)
- 11-24 : vlan 1 (Defaut)

Plage d'adressage : 172.16.vlan.x


Le pc 1 est branché sur le port 13(vlan1), je le modifie et le branche sur le port 3(vlan10)

Afin de valider la configuration des ordinateurs derrières le switchs, j'ai configuré des adresses ip fixes  

- PC 11 : 172.16.10.10 
- PC 1 : 172.16.10.20
- PC 12 : 172.16.10.30

Ce paramétrage est temporaire, le temps que le serveur dhcp fonctionne.

J'ai utilisé ping pour m'assurer que les 3 pc communique bien.


### Routeur R-bat1

 On constate que pc09(192.168.10.10) ne peut pinguer pc11 (172.16.10.10)

sur S1 j'ai modifié le port gig0/1 pour activer le mode trunk et laisser passer les vlans (10,20 et 99)
sur S2 j'ai modifié le port gig0/2 pour activer le mode trunk et laisser passer les vlans (10,20 et 99)

je constate maintenant que le ping passe bien entre les machines derriere le s1 et les machines derriere le s2.

## Datacenter
...
## Batiment 2

Les 3 PC sont branchés sur les ports 1-5 du switch.

PC 13 : Fa0/1

PC 2 : Fa0/4

PC 14 : Fa0/2

Tous configurés en VLAN 10 (TECH).

  
  
   Le laptop d’administration était en VLAN1, modifié en VLAN99 (Management).

les ordinateurs derriere le switches s1-bat2 communique entre eux. 

les ordinateurs ne communique pas entre eux 

- PC13 (Fa0/1) → 192.168.30.10 

- PC2 (Fa0/4) → 192.168.30.20

- PC14 (Fa0/2) → 192.168.30.30 

- PC15 → 192.168.30.40 

- PC3 → 192.168.30.50 

- PC16 → 192.168.30.60 
### Swi@ch S1-bat2

-  il manquait un vlans (99) dans configuration du 1er switch le mode trunks etais deja actif (giga0/1).
- les ordi communique entre eux


### Swi@ch S2-bat2

-  le port giga0/2 etais activé en trunks mais n'etais pas configurer de maniere a pouvoir autorisé les vlans


- les ordinateurs communiqe entre eux mais en inter switch























interface gigabitEthernet 0/1
 no shutdown
exit

interface gigabitEthernet 0/1.10
 encapsulation dot1Q 10
 ip address 192.168.30.1 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/1.20
 encapsulation dot1Q 20
 ip address 192.168.40.1 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/1.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/2
 no shutdown
exit

interface gigabitEthernet 0/2.10
 encapsulation dot1Q 10
 ip address 192.168.30.2 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/2.20
 encapsulation dot1Q 20
 ip address 192.168.40.2 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/2.99
 encapsulation dot1Q 99
 ip address 192.168.99.2 255.255.255.0
 no shutdown
exit

