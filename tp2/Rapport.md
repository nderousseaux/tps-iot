# Rapport TP2 - IOT

## 1. Compilation

Pour commencer on compile les firmwares FTD et MTD sur le channel 12 avec le PANID `0x12f0/`

```bash
$ iotlab-auth -u iot2023stras6
$ git clone https://github.com/RIOT-OS/RIOT.git
$ cd RIOT
$ git checkout 2022.07
$ cd examples/openthread
$ source /opt/riot.source
$ make BOARD=iotlab-m3 OPENTHREAD_CHANNEL=12 OPENTHREAD_PANID=0x12f0 OPENTHREAD_TYPE=ftd
$ cp bin/iotlab-m3/openthread.elf openthread-ftd.elf
$ source /opt/riot.source
$ make BOARD=iotlab-m3 OPENTHREAD_CHANNEL=12 OPENTHREAD_PANID=0x12f0 OPENTHREAD_TYPE=mtd
$ cp bin/iotlab-m3/openthread.elf openthread-mtd.elf
```

Les deux binaires sont maintenant trouvable dans `~/RIOT/examples/iotlab-m3`.

## 2. Déploiement du réseau

Une fois les 5 noeuds déployés avec les bon firmawares (2 sur FTD et 3 sur MTD), on peut leur définir leur masterkey :

```bash
$ serial_aggregator
$ ifconfig down
$ thread stop 
$ masterkey 00cafedeca00deadbeaf00deafbee000
$ ifconfig up
$ thread start 
```

On va pouvoir sniffer le réseau afin de comprendre le fonctionnement de :

1. l'election du leader
2. l'election des parents sur les noeuds End-Device
3. l'attribution des adresses (RLOC, IPv6)

Pour cela :

```bash
$ ssh iot-lab sniffer_aggregator -i 382202 -r -d -o - | wireshark -k -i -
```

On renseigne égallement la clé de décryptage `masterkey : 00cafedeca00deadbeaf00deafbee000` dans wireshark. 

### 1. Élection du leader

Dès leur allumage, les noeuds de type FTD (chez nous`m3-12` et `m3-14`) commencent à broadcast leur `Link request` pour s'annoncer à l'autre FTD.

![image](Capture/Link_request.png)

Chaque routeur leader (chez nous `m3-14`) répond à l'autre FTD avec avec un `Link Request + Link Accept`.

![LR+LA](Capture/LR_LA.png)

Pour finir, le routeur non leader (chez nous, `m3-12`) valide l'élection avec un `Link Accept`. 

### 2. Élection des parents sur les noeuds End-Device

L'élection des parents est assez similaire à l'élection du leader. 

Dès leur allumage, les noeuds de type MTD (chez nous `m3-10`,`m3-11` et `m3-13` )  commencent à broadcast leur `Parent request` pour s'annoncer aux FTD.

Dans les captures d'écran suivantes, nous verrons l'établissement du lien entre `m3-11` et le routeur `m3-14`.

![Parent request](Capture/Parent_request.png)

Ensuite, les routeurs ayant reçu la `Parent request` répondent au MTD avec un `Parent response`. 

![Parent Response](Capture/Parent_Response.png)

Ensuite, le noeud MTD renvoie au parent qu'il aura choisi un `Child ID request` pour récupérer un identifiant. 

![Child ID Request](Capture/ChidIDRequest.png)

Et pour finir, le FTD élu comme parent répondra avec un `Child ID response` pour donner le nouvel identifiant à l'enfant.

![Child ID Response](Capture/ChildID_Response.png)

### 3. Attribution des adresses (RLOC, IPv6)

Lors de l'envoi du `Child ID Request` le MTD demande l'adresse `RLOC16` au routeur :

![Requiest](Capture/Rloc16request.png)

Lors de l'envoi du `Child ID Response` à son enfant, le FTD fourni une adresse `RLOC16` au MTD :

![Addr](Capture/RLOC16addr.png)

## 3. Plan du réseau

Grace aux commandes `status ` et `child table` ainsi qu'avec les captures, on peut représenter le schéma de réseau suivant :

```mermaid
graph LR
14[(<b>m3-14<br/><i>RLOC: 98:00<i/><br/>IPv6: fe80::58c8:9e6b:d100:621</b><br/>leader<br/><i>FTD</i>)]
12[(<b>m3-12<br/><i>RLOC: 98:03<br/>RLOC: 60:00<br/>IPv6: fe80::30a2:adac:ee76:55df<i/></b><br/>routeur<br/><i>FTD</i>)]
10[<b>m3-10<br/><i>RLOC: 60:04<br/>IPv6: fe80::4086:9b4f:ebdb:d0fd<i/></b><br/>child<br/><i>MTD</i>]
11[<b>m3-11<br/><i>RLOC: 98:04<br/>IPv6: fe80::d880:6793:7b77:453f<i/></b><br/>child<br/><i>MTD</i>]
13[<b>m3-13<br/><i>RLOC: 60:05<br/>IPv6: fe80::d8d2:d5c6:5117:744b<i/></b><br/>child<br/><i>MTD</i>]

14 --- 11
14 --- 12
12 --- 10
12 --- 13
```

On peut constater que les noeuds `m3-10` et `m3-13` ne se sont pas appareillés avec le leader `m3-14`, mais avec le second routeur `m3-12`. Cela peut s'expliquer simplement : la connection entre `m3-14` et  `m3-10`/`m3-13` est sans doute plus difficile, à cause des perturbations radios, ou de la distance entre eux par exemple. Et en effet, en faisant des tests avec `ping` on constate que les temps de réponses sont significativement plus lent entre `m3-14` et  `m3-10`/`m3-13`  que entre `m3-12` et  `m3-10`/`m3-13`.

## 4. ICMPv6

> **Note:** À partir de là, le travail à été effectué sur  d'autres noeuds, il est normal si les addresses ou les identifiants sont différents par rapport à la première partie du rapport.

Le connectivité entre nos noeuds est parfaitement fonctionnelle. 

La commande suivante permet de pinger un de nos noeuds :

```bash
$ nc m3-10 20000
```

Le résultat est visible dans la capture wireshark :

![Ping](/Users/nderousseaux/code/tps/iot/tp2/Capture/Ping.png)


## 5. Connectivité UDP (CoAP)

Pour commencer, on doit créer la ressource :

![Coap_start1](/Users/nderousseaux/code/tps/iot/tp2/Capture/coap/start1.png)

La seconde étape est de récuperer la ressouce :

![Coap_start2](/Users/nderousseaux/code/tps/iot/tp2/Capture/coap/Start2.png)

La capture wireshark nous montre que le transfert UDP CoAP c'est déroulé sans encombre :

![UDP COAP](/Users/nderousseaux/code/tps/iot/tp2/Capture/coap/Udp_coap.png)

On constate aussi que les messages ne sont pas assez long pour nécessiter une fragmentation. Ils ont néanmoins été émis plusieurs fois.

## 6. Changement de leader

Dans cette question, on considérera la structure suivante :

```mermaid
graph LR
53[<b>m3-53<br/>child<br/></b><i>MTD</i>]
54[<b>m3-54<br/>child<br/></b><i>MTD</i>]
55[(<b>m3-55<br/>leader<br/></b><i>FTD</i>)]
56[<b>m3-56<br/>child<br/></b><i>MTD</i>]
57[(<b>m3-57<br/>routeur<br/></b><i>FTD</i>)]

55 --- 53
55 --- 54
55 --- 56
55 --- 57
```



Pour commencer on éteint le noeud leader `m3-55`.

On remarque que le second FTD, le noeud `m3-57` envoie plusieurs message  `MLE Advertissement` avant de détecter l'absence de leader. 
![Capture du spam des MLEadv](/Users/nderousseaux/code/tps/iot/tp2/Capture/Q6/Extinctionde55.png)

*m3-55 est éteint à partir du paquet marqué*. 

Dès que `m3-57` à détecter l'absence de leader, `m3-57` commence à broadcaster des `Parents request`.  
![Prends ce ratio NATHA](/Users/nderousseaux/code/tps/iot/tp2/Capture/Q6/Parent_req.png)

Les MTD childs (`m3-XX`, `m3-XX` et `m3-XX`) tentent de joindre l'ancien leader `m3-55`, sans succès.

Apres quelques Data Response, les childs essayent de joindre leur encien leader mais en vain en envoyant des `Child Update Request`

![MessagepourR2](Capture/Q6/MesspourR2.png)

Les MTD ayant remarqué l'absence de leader, commencent à envoyer des `Parents Request`. 
![Parent request](Capture/Q6/Parent_R.png)

À partir de là, cela se déroule comme une élection de parent classique. Les MTD vont donc recevoir un `Parent Response` de `m3-57` qui c'est autoproclamé leader.
![Parent request](Capture/Q6/P_response.png)

Et ensuite, comme dans une élection de parents classique, débute l'échange de `Child ID request` et de  `Child ID response` 
![Child request & Response](/Users/nderousseaux/code/tps/iot/tp2/Capture/Q6/Child_Rs.png)

Assez naturellement, le réseau à maintenant cette topologie :

```mermaid
graph LR
53[<b>m3-53<br/>child<br/></b><i>MTD</i>]
54[<b>m3-54<br/>child<br/></b><i>MTD</i>]
56[<b>m3-56<br/>child<br/></b><i>MTD</i>]
57[(<b>m3-57<br/>leader<br/></b><i>FTD</i>)]

57 --- 53
57 --- 54
57 --- 56
```