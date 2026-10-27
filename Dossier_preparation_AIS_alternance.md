# Dossier technique - Préparation entretien AIS en alternance - Syltux

**Analyse statique du brief  : Synthèse des consignes, critères d'évaluation, analyse des topologies réseaux, plan de réalisation**  
**Date de finalisation : 27 octobre 2026**

## Demande

Les documents de brief demandent un mini-projet sous Cisco Packet Tracer en deux étapes : 
1 - relier deux LAN via un routeur en adressage statique, vérifier le routage par ping
2 - puis ajouter à la configuration existante un serveur DHCP dans le LAN du site A pour servir les deux sous-réseaux grâce à deux pools et à un relais DHCP (« IP Helper »). Il faut aussi remettre le fichier .pkt et une liste d’au moins deux menaces, en sachant expliquer les composants, leurs réglages, leur utilité et le fonctionnement des menaces.

> **À retenir.** État observé dans les éléments fournis : 

- topologie finale fournie pour chacun des deux scénarios qui servira de base pour le schéma réseau et le plan d'adressage ainsi que son implémentation via configuration IOS au CLI
- test de recette demandé (ping inter-LAN)
- pools DHCP et configuration de DHCP relay. 

Challenge: Le principal point technique à verrouiller est le DHCP inter-réseaux : le serveur doit avoir une adresse fixe dans le LAN A et la commande ip helper-address doit être placée sur l’interface du routeur côté LAN B, avec l’adresse IP exacte du serveur. Le pool du LAN B doit annoncer la passerelle 192.168.2.1.

## 1. Sources statiques examinées et limites

- Consignes candidat AIS alternance.
- Brief projet : 2 LAN et 1 Routeur, dont les schémas des parties 1 et 2 et les critères d’évaluation.

La consigne candidat contient littéralement deux puces incomplètes/incompréhensibles (« erg » et « ert ») après « compétences sur ».
Elles semblent tronquées, il n’est pas possible de leur attribuer un sens fiable sans spéculer. 
La consigne explicite en revanche l’autonomie d’apprentissage et l’usage des outils numériques de base. 

## 2. Exigences de l’entretien et du mini-projet

### Préparation demandée

- S’inscrire sur Khan Academy et suivre les quatre premiers chapitres gratuits de The Internet : introduction à Internet, connecter les réseaux, adresses et Internet, routage.
- S’inscrire sur Cisco Networking Academy et suivre les deux premiers cours d’introduction à Packet Tracer.
- Disposer d’un ordinateur et d’une connexion Internet. Certaines ressources sont en anglais; les outils de traduction automatique sont autorisés.
- Démontrer une posture d’apprenant autonome. Être bloqué ne constitue pas à lui seul un motif de rejet; l’aide peut être demandée, sans déléguer le travail.

### Contexte et objectifs métier

Scénario : administrer le réseau d’une PME et concevoir l’interconnexion de deux LAN (site A et site B) via un routeur. Objectifs déclarés : installer et manipuler Packet Tracer, créer un LAN, interconnecter les deux LAN, puis identifier des menaces cyber courantes pour cette configuration.

Durée estimée par le brief : 0,5 à 1 journée.

### Partie 1 — adressage statique

- Reproduire la topologie illustrée : Site A (192.168.1.0/24) et Site B (192.168.2.0/24), chacun raccordé à son commutateur et à un routeur; les schémas montrent des terminaux sur chaque LAN (2 par LAN).
- Configurer chaque composant avec des adresses IP statiques cohérentes. Les interfaces routeur assurent les passerelles et le routage entre les deux sous-réseaux (pattes LAN).
- Valider le fonctionnement en envoyant des pings entre des terminaux situés dans les deux LAN.
- Lister au moins deux menaces courantes liées à ce réseau.

### Partie 2 — attribution DHCP

- Modifier la topologie selon le schéma 2 et ajouter un serveur DHCP dans le LAN du site A.
- Activer/configurer le serveur et créer deux pools d’adresses, un par sous-réseau.
- Configurer le DHCP relay (« IP Helper ») sur l’interface de passerelle du sous-réseau distant pour acheminer les requêtes vers le serveur.
- Configurer les composants destinés à recevoir leur adresse en DHCP et vérifier adresse, masque, passerelle, puis la connectivité inter-LAN.

### Livrables explicitement exigés

- La topologie Packet Tracer enregistrée au format .pkt.
- Une liste des menaces identifiées.


## 3. Critères d’évaluation

1. La topologie réalisée respecte la demande.
2. Tous les composants de la topologie sont identifiés; leur rôle respectif est défini sans erreur.
3. Les configurations appliquées à chaque composant sont expliquées sans erreur et l’intérêt de chaque configuration est explicité.
4. Au moins deux menaces courantes sont identifiées.
5. Le fonctionnement général de chaque menace est expliqué sans erreur.

Implication pratique : ne pas se limiter à montrer une maquette ou une série de commandes. Pouvoir expliquer le chemin d’un paquet entre les LAN, la fonction de la passerelle et du routeur, le rôle du serveur DHCP et la raison du relais, puis interpréter les résultats de tests.


## 4. Configuration cible recommandée (base de vérification)

Plan d’adressage cohérent avec les plages indiquées dans le brief et les bonnes pratiques de segmentation réseau. 

| Équipement / usage | Adresse / paramètres cibles | Remarque |
| --- | --- | --- |
| Routeur — passerelle LAN A | 192.168.1.1 /24 | Interface raccordée au switch du site A; interface activée. |
| Routeur — passerelle LAN B | 192.168.2.1 /24 | Interface raccordée au switch du site B; interface activée. |
| Serveur DHCP (site A) | 192.168.1.10 /24; passerelle 192.168.1.1 | Adresse statique hors plage distribuée. |
| Pool DHCP LAN A | Réseau 192.168.1.0/24; début 192.168.1.20; passerelle 192.168.1.1 | La plage doit exclure les adresses fixes; nombre de baux selon le besoin. |
| Pool DHCP LAN B | Réseau 192.168.2.0/24; début 192.168.2.20; passerelle 192.168.2.1 | Idem |
| Relais DHCP LAN B | ip helper-address 192.168.1.10 | À configurer sur l’interface routeur recevant les diffusions DHCP du LAN B, pas sur l’interface du serveur. |

Exemple IOS :

```text
enable
configure terminal
interface GigabitEthernet0/0/1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
interface GigabitEthernet0/0/2
 ip address 192.168.2.1 255.255.255.0
 ip helper-address 192.168.1.10
 no shutdown
end
show ip interface brief
show running-config
```

Dans Packet Tracer, l’interface DHCP du serveur doit être activée. Le serveur conserve son adresse statique; les PC destinés aux baux sont réglés sur DHCP. Un routeur connecté directement aux deux réseaux connaît les deux réseaux comme directement connectés; aucune route statique additionnelle n’est normalement requise dans ce scénario de base.

> **À retenir.** Pourquoi le relay? DHCP commence par des diffusions locales (broadcast), qui ne traversent pas le routeur. L’IP Helper transforme/transmet la requête du LAN B vers le serveur DHCP; le serveur choisit le pool correspondant au réseau du client et répond via le relais.

## 5. Validation finale — liste de contrôle

- [ ] Topologie conforme au schéma du brief : 2 LAN distincts, 1 routeur; deux switches et les terminaux prévus; serveur en LAN A pour la partie 2.
- [ ] Aucune interface du routeur n’est configurée dans le mauvais sous-réseau; liens physiques et interfaces sont up/up.
- [ ] Chaque terminal en statique (partie 1) a une IP unique, masque /24 correct et passerelle de son LAN.
- [ ] Pings réussis sans perte : PC LAN A → sa passerelle, PC LAN B → sa passerelle, puis PC A ↔ PC B. Un premier délai peut arriver pendant la résolution ARP; répéter et reporter le résultat final.
- [ ] Serveur DHCP joignable à l’adresse fixe 192.168.1.10; service DHCP activé; deux pools configurés, avec masques et passerelles correspondant à leurs sous-réseaux.
- [ ] ip helper-address configuré côté LAN B vers l’adresse du serveur; confirmer dans show running-config.
- [ ] Client LAN A et client LAN B en DHCP reçoivent chacun une adresse de la bonne plage, masque /24 et bonne passerelle. Le bail LAN B doit recevoir 192.168.2.1 comme passerelle.
- [ ] Tester les pings à nouveau lorsque les PC sont en DHCP; consigner IP/mask/gateway de chaque client et le résultat des pings.
- [ ] Enregistrer le fichier .pkt après les derniers changements, le rouvrir pour vérifier l’état sauvegardé. Joindre une liste de menaces.

Commandes de dépannage utiles : show ip interface brief, show running-config, ipconfig (sur PC Packet Tracer), ping <adresse>, et vérification de l’onglet Services > DHCP du serveur. Examiner le masque, la passerelle, l’interface ciblée par helper, l’état du service et l’absence d’adresses qui se chevauchent.

## 6. Menaces rassemblées (sélection)

| Menace | Fonctionnement général / impact | Atténuations de base |
| --- | --- | --- |
| Serveur DHCP pirate (rogue DHCP) | Un appareil non autorisé répond aux requêtes DHCP avant le serveur légitime et fournit une fausse passerelle ou un DNS contrôlé; le trafic peut être détourné, intercepté ou coupé. | DHCP snooping sur les switches compatibles; ports de confiance limités au serveur/liaison prévue; contrôle des équipements et surveillance des baux. |
| Usurpation ARP / attaque de l’intercepteur (MITM) | Un attaquant du LAN envoie de fausses associations IP–MAC (par exemple en se faisant passer pour la passerelle) afin de faire transiter ou perturber le trafic. | Dynamic ARP Inspection avec DHCP snooping lorsque disponible; segmentation et contrôle d’accès; chiffrement applicatif (TLS/VPN); surveillance des anomalies ARP. |
| Déni de service par épuisement DHCP | Un attaquant génère de nombreuses requêtes ou identifiants MAC pour consommer le pool d’adresses; les clients légitimes ne peuvent plus obtenir de bail. | DHCP snooping avec limites de débit, port-security/contrôle des terminaux, taille de pool adaptée, baux/réservations surveillés et alertes. |
| Accès non autorisé / compromission d’un poste | Un terminal compromis ou une personne connectée au LAN tente d’accéder aux autres appareils ou d’exploiter des services mal protégés. | Contrôle d’accès, mises à jour, pare-feu des hôtes, mots de passe robustes, privilèges minimaux, segmentation et journalisation. |
