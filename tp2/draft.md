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

Tout d'abord on commence avec la structure suivante : 
```
m3-53;child
m3-54;child
m3-56;child
m3-55;leader
m3-57;router
```

Puis on éteint le noeud leader
On remarque que 57 envoie plusieurs MLE adv, avant de detecter l'absence de leader  
*55 est éteint à partir du paquet marqué*
![Capture du spam des MLEadv](Capture/Q6/Extinctionde55.png)

57 detecte l'absense de leader donc commence à envoyer des parents request
![Prends ce ratio NATHA](Capture/Q6/Parent_req.png)

Apres quelques Data Response, les childs essayent de joindre leur encien leader mais en vain. 
Avec un Childupdaterequest
![messpourR](Capture/Q6/MesspourR1.png)

On le voit bien ici

![MessagepourR2](Capture/Q6/MesspourR2.png)

Certains commencent alors à detecter l'absence de leader donc commencent à envoyer des parents requests
![Parent request](Capture/Q6/Parent_R.png)

Il vont ensuite recevoir Parent response de 57, qui s'est autoproclamé leader. 
![Parent request](Capture/Q6/P_response.png)

C'est alors que debute l'echange de child ID request et response
![Child request & Response](Capture/Q6/Child_Rs.png)

C'est ainsi que 54 deviens un child de 57 qui est à present le leader. 
On a pris l'exemple de 54 pour les captures mais c'est pareil pour les autres aussi. (53, 54 et 56)

# Question 7

[Tuto utils](https://www.iot-lab.info/learn/tutorials/riot/riot-public-ipv6-a8-m3/)