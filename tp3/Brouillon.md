# Rapport 

## Question 1 

Protocole : CSMA ContikiMAC
Les collisions

## Question 3

On peut voir le noeud numero 2 envoie des paquets en continue jusqu'à la reception de l'ACK.
![Envoie de paquet depuis 2](Capture/Timeline_2.png)

Les messages rouges c les messages pas reçu alors que le noeud est allumé en reception. 
![pas reçu](Capture/Pasrecul.png)

## Question 2 

La communication commence par un broacast de tous les noeuds qui broadcast DIS

![DIS](Capture/DIS.png)

La racine envoie le premier DIO. Il s'agit du noeud 1

![1erDIO](Capture/1erDIO.png)

Les autres noeuds des DIO pour que les noeuds plus bas puisse rejoindre le réseau. 

![AutreDIO](Capture/AutreDIO.png)

Les noeuds envoie alors un DAO à leur parent. 
Le DAO (Destination Advertisement Object) transporte des informations sur les noeuds que l’émetteur peut joindre.

![DAO](Capture/DAO.png)

Racine : 1
Rank : 256

1er à rejoindre : 5
Rank : 896

## Question 3


