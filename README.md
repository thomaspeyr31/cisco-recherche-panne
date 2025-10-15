# Exercice

Vlan10 "tech"
Vlan20 "Admin"
Vlan99 "Management"
Vlan100 : "Server"


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

De façon provisoire, j'ai mis les PC derrière S1Bat2 et S2Bat2 en IP Fixe afin de vérifier qu'ils communiquent bien entre eux :

- PC13 (Fa0/1) → 192.168.30.10 

- PC2 (Fa0/4) → 192.168.30.20

- PC14 (Fa0/2) → 192.168.30.30 

- PC15 → 172.31.10.10

- PC3 → 172.31.10.20

- PC16 → 172.31.10.30


### Swi@ch S1-bat2

-  il manquait un vlans (99) dans configuration du 1er switch le mode trunks etais deja actif (giga0/1).


### Swi@ch S2-bat2

-  le port giga0/2 etais activé en trunks mais n'etais pas configurer de maniere a pouvoir autorisé les vlans


les ordinateurs de batiment 2 communiquent entre eux via Rbat2


## Communication des batiments sur le DC

Sur RBat2, il n'y a pas de route vers le vlan des serveurs (192.160.100.0), je la rajoute :

enable
conf t
ip route 192.168.100.0 255.255.255.0 175.89.46.1
end

Sur RDC, il faut mettre en place les routes retours vers les vlan du batiment1 et bat2 :

enable
configure terminal
ip route 192.168.30.0 255.255.255.0 175.89.46.2
ip route 192.168.40.0 255.255.255.0 175.89.46.2
ip route 192.168.50.0 255.255.255.0 175.89.46.2
ip route 172.31.10.0 255.255.255.0 175.89.46.2
ip route 172.31.20.0 255.255.255.0 175.89.46.2
ip route 172.31.99.0 255.255.255.0 175.89.46.2
ip route 192.168.99.0 255.255.255.0 200.165.54.1
ip route 172.16.10.0 255.255.255.0 200.165.54.1
ip route 172.16.20.0 255.255.255.0 200.165.54.1
ip route 172.16.99.0 255.255.255.0 200.165.54.1
end

Vérification : 

Depuis un PC qui est derrière RBat2, je pingue bien le routeur RDC (ping 175.89.46.1).
Par contre, je ne pingue toujours pas le serveur DHCP : (ping 192.168.100.30).
Il y a donc un problème entre RDC et switch S-DATACENTER.

RDC est branché sur le switch du réseau 192.168.100.0 sur un port (Gi0/2) associé au vlan 1, alors que les trois serveurs sont sur le vlan 100 : port 1,2 et 3):
J’associe le  port Gi0/2 du switch S-DATACENTER au vlan 100 (je peux le laisse en mode ACCESS car il n’y a qu’un seul vlan)

Maintenant les PCs de bat2 pinguent bien le dhcp (ou le serveur web ou le serveur dns)

Sur RBat1, je rajoute la route vers 192.168.100.0 :

conf t
ip route 192.168.100.0 255.255.255.0 200.165.54.2
end

Maintenant les PCs de bat1 pinguent bien le dhcp (ou le serveur web ou le serveur dns)

----------------
Afin de pouvoir tester, tous les PCs étaient en IPfixes. Le réseau étant maintenant validé jusqu'au DHCP, je les bascule en dhcp -> Ca ne fonctionne pas.

C'est normal, la requete dhcp n'est pas relayé par Rbat1 et Rbat2 vers le serveur DHCP (192.168.100.30).

Sur RBat1 :

Sur RBat 1 : 
configure terminal
!
! --- VLANs venant de S1 ---
interface GigabitEthernet0/1.10
 description VLAN10 - TECH (S1)
 ip helper-address 192.168.100.30
!
interface GigabitEthernet0/1.20
 description VLAN20 - ADMIN (S1)
 ip helper-address 192.168.100.30
!
! --- VLANs venant de S2 ---
interface GigabitEthernet0/2.10
 description VLAN10 - TECH (S2)
 ip helper-address 192.168.100.30
!
interface GigabitEthernet0/2.20
 description VLAN20 - ADMIN (S2)
 ip helper-address 192.168.100.30
!
end
write memory


Sur RBat 2 :

enable
configure terminal
!
! --- VLANs venant de S1 (réseau 192.168.x) ---
interface GigabitEthernet0/1.10
 description VLAN10 - TECH (S1)
 ip helper-address 192.168.100.30
!
interface GigabitEthernet0/1.20
 description VLAN20 - ADMIN (S1)
 ip helper-address 192.168.100.30
!
! --- VLANs venant de S2 (réseau 172.31.x) ---
interface GigabitEthernet0/2.10
 description VLAN10 - TECH (S2)
 ip helper-address 192.168.100.30
!
interface GigabitEthernet0/2.20
 description VLAN20 - ADMIN (S2)
 ip helper-address 192.168.100.30
!
end

Maintenannt que les requetes sont DHCP sont relayés jusqu'au serveur DHCP, il faut écrire les regles d'attribution des IP en fonctio ndu vlan d'origine.
Tous les PC devront avoir 192.168.100.20 comme serveur DNS.

Dans un premier temps on va paramétrer le service DHCP du serveur DHCP.

4 pools vont etre créés :

Si une demande DHCP provient de :
	- 192.168.10.1 (S1bat1)
		On retourne une IP à partir de 192.168.10.10 (place de 10 IP)
	- 192.168.20.1 (S1bat1)
		On retourne une IP à partir de 192.168.20.10 (place de 10 IP)
	- 192.168.30.1 (S1bat2)
		On retourne une IP à partir de 192.168.30.10 (place de 10 IP)
	- 192.168.40.1 (S1bat2)
		On retourne une IP à partir de 192.168.40.10 (place de 10 IP)
	- 172.31.10.1 (S2bat2)
		On retourne une IP à partir de 172.31.10.10 (place de 10 IP)
	- 172.31.20.1( S2bat2)
		On retourne une IP à partir de 172.31.20.10 (place de 10 IP)
	- 172.16.10.1( S1bat1)
		On retourne une IP à partir de 172.16.10.10 (place de 10 IP)
	- 172.16.20.1( S2bat1)
		On retourne une IP à partir de 172.16.20.10 (place de 10 IP)


-> Les PCs récupèrent bien une adresse IP valide depuis le serveur DHCP.

Je ne sais pas si c’était une erreur dans le sujet ou si c’est moi qui ai fait une fausse manip, mais il y a un helper parasite sur RBat1, on le supprime :

conf t
interface GigabitEthernet0/1
 no ip helper-address 192.168.100.31
end
write memory

On passe le port Gi0/1 de S1 en truck et on accepte tous les vlan

——————
Le DNS est mal paramètre:

Les record Cisco.com et www.cisco.com référence l’ip 192.168.100.11, alors que l’IP du serveur web est 192.168.100.10. Je fais la modification, et je teste sur n’importe quel PC que le navigateur se connecte à www.cisco.com

















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

## Bâtiment 