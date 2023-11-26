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
Oui 

- Dans childID request, il demande une adresse RLOC16
![Requiest](Capture/Rloc16request.png)

- Dans ChildID response il envoie l'adresse RLOC16
![Addr](Capture/RLOC16addr.png)

C'est ça 
![rloc16](Capture/rloc16.png)



## Question 4 : ICMPv6 

On fait un ping de tous les noeuds vers un autre et on remarque que le ping passe bien

- Exemple de commande
![Commande](Capture/cmd_ping.png)

La capture
![Ping](Capture/Ping.png)

## Question 5 : UDP (COAP)

- Tout d'abord on crée la ressource
![Coap_start1](Capture/coap/start1.png)

On résupere la ressource test dans le serveur coap crée precedement
![Coap_start2](Capture/coap/Start2.png)

- La capture nous montre que la communication s'est déroulé sans probleme
![UDP COAP](Capture/coap/Udp_coap.png)

Non les messages ne sont pas fragementé. Ils sont juste retransmis plusieurs fois

# Question 6

Leader : 28
Router : 29

