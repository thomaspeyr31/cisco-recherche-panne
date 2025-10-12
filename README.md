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

Afin de valider la configuration des ordinateurs derrieres le switchs, j'ai configuré des adresses ip fixes  

- PC 09 : 192.168.10.10 
- PC 00 : 192.168.10.20
- PC 10 : 192.168.10.30

Ce paramétrage est temporaire, le temps que le serveur dhcp fonctionne.

J'ai utilisé ping pour m'assurer que les 3 pc communique bien.

#### Laptop de management

L'ordinateur laptop est l'ordinateur d'un administrateur reseaux ayant vocation a utilisé le vlan 99

On decide de mettre une ip fixe car cet un reseau de management. "192.168.99.10"


### Swi@ch S2-bat1

Les ports : 
- 1-5 : vlan 10 (tech)
- 6-10 : vlan 20 (Admin)
- 11-24 : vlan 1 (Defaut)

Plage d'adressage : 172.16.vlan.x


Le pc 1 est branché sur le port 13(vlan1), je le modifie et le branche sur le port 3(vlan10)

Afin de valider la configuration des ordinateurs derrieres le switchs, j'ai configuré des adresses ip fixes  

- PC 11 : 172.16.10.10 
- PC 1 : 172.16.10.20
- PC 12 : 172.16.10.30

Ce paramétrage est temporaire, le temps que le serveur dhcp fonctionne.

J'ai utilisé ping pour m'assurer que les 3 pc communique bien.


### Routeur R-bat1




Batiment 2
mauvais branchement (actuellement port 20 possibilité mettre port 24 (vlan99))

## Datacenter
...
## Batiment 2
...