# Brouillon rapport

## Election du leader 

On remarque :  
m3-14;58c89e6bd1000621  
m3-12;30a2adacee7655df  

A l'allumage des FTD les routeurs broacasts des messages Link request pour entrer en contact avec les autres routeurs. 

![image](Capture/Link_request.png)

Le routeur repond avec Link Request + Link Accept 

![LR+LA](Capture/LR_LA.png)

m3-12 finit l'election avec un dernier Link Accept

![Link accept](Capture/Link_Accept.png)

## Election des parents sur les noeuds END-device (avec m3-11 par exemple)

Les noeuds rejoignant le réseau commencent à broadcast *Parent request*

![Parent request](Capture/Parent_request.png)

Puis Les routeurs ayant reçu le message précédent répondent avec Parent Response

![Parent Response](Capture/Parent_Response.png)

Le noeud envoie ensuite Child ID Request au parent choisi

![Child ID Request](Capture/ChidIDRequest.png)

Enfin le parent repond avec un Child ID Response.

![Child ID Response](Capture/ChildID_Response.png)

## Attribution des adresses (RLOC, IPv6)

L'adresse RLOC est fournie par le parent à l'envoie du message Child ID Response. 
L'IPV6 ? 

## ICMPv6 

