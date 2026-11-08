# Chapitre 1 — Choisir entre échanges synchrones et asynchrones

## 1. Une interaction ne se résume pas à son protocole

Le tome II a construit une application capable de recevoir une demande, de protéger ses règles et d’exposer une information compréhensible. Dès que cette application collabore avec un partenaire, un stockage ou un consommateur, elle dépend de comportements qu’elle ne contrôle pas entièrement.

Ce tome examine ces frontières d’intégration. Il ne cherchera pas à éliminer les défaillances par une technologie unique. Il cherchera à préciser ce qui est promis, ce qui est connu et comment reprendre lorsque les étapes ne réussissent pas ensemble.

Le choix synchrone ou asynchrone constitue un premier arbitrage. Il influence le parcours utilisateur, les ressources, la charge et l’exploitation. Il ne suffit pas de demander si les équipes préfèrent HTTP ou un broker.

Dans notre cas fictif de paiements, la consultation d’un fait connu, la prise en charge d’une demande et l’établissement d’un résultat externe sont des interactions différentes. Elles peuvent nécessiter des réponses et des temporalités différentes.

## 2. Distinguer trois dimensions

La première dimension concerne le contrat métier. L’appelant attend-il le résultat final avant de poursuivre, ou accepte-t-il une prise en charge suivie d’un résultat ultérieur ?

La deuxième concerne la réalisation du programme. Un thread peut attendre de manière bloquante ; une bibliothèque peut libérer ce thread en attendant une réponse. Cette optimisation ne transforme pas nécessairement le contrat métier.

La troisième concerne le transport et la conservation. Un appel direct, une file ou un journal possèdent des mécanismes différents. Leur emploi ne détermine pas automatiquement le sens de l’engagement applicatif.

| Situation | Ce qu’elle montre |
|---|---|
| Client HTTP non bloquant qui attend le résultat final | Réalisation asynchrone, contrat toujours fondé sur l’attente du résultat |
| API HTTP qui reçoit puis expose une opération à consulter | Réception synchrone et traitement métier différé |
| Message avec réponse attendue immédiatement | Transport par messages, couplage temporel toujours présent pour l’appelant |
| Événement conservé puis consommé plus tard | Découplage temporel, selon les garanties de conservation |

Microsoft décrit précisément un pattern où une API répond rapidement à une demande puis fournit un endpoint de suivi d’un travail réalisé séparément. [1]

## 3. Le contrat synchrone : connaître un résultat dans la même interaction

Un échange synchrone permet à l’appelant de poursuivre à partir d’une réponse obtenue dans l’interaction en cours. Cette réponse peut être une information, une décision ou un refus explicite.

Pour consulter un paiement déjà connu, ce mode est souvent facile à comprendre : le client demande une représentation et obtient le résultat disponible selon son contrat de fraîcheur.

Il faut néanmoins préciser la portée. Une réponse rapide du système de suivi ne prouve pas que la copie est récente. Une réponse du partenaire n’établit une exécution que si le contrat lui donne ce sens.

L’attente consomme aussi des ressources, directement ou indirectement. Une API non bloquante réduit certaines occupations de threads sans rendre illimités les connexions, les buffers ou la capacité des dépendances.

AWS recommande de borner les appels distants et explique comment les attentes prolongées peuvent épuiser les ressources. Le choix d’un timeout doit tenir compte des latences observées et de ce que le mécanisme couvre réellement, notamment l’établissement des connexions. [2]

## 4. Le traitement différé : assumer un résultat ultérieur

Un contrat asynchrone sépare la prise en charge et le résultat. L’appelant doit pouvoir comprendre ce qui est acquis, comment retrouver le travail et quelles situations restent possibles.

```text
Client → API : soumettre une demande
API → Client : prise en charge et référence de suivi

Traitement → Partenaire : réaliser l’interaction prévue
Traitement → État durable : résultat ou situation à résoudre

Client → API de suivi : consulter le dernier état connu
```

Le pattern Asynchronous Request-Reply propose notamment une réponse 202 et une référence de consultation pour les traitements longs. Il prévoit également la validation des demandes avant leur mise en traitement. [1]

Le code 202 ne fournit cependant pas, par lui-même, une garantie de durabilité ou de succès futur. HTTP précise que l’acceptation pour traitement ne signifie pas achèvement. [3]

Dans notre conception, l’engagement de prise en charge doit être défini explicitement : informations conservées, possibilités de reprise et limites. Si le client perd la réponse, il doit disposer d’un moyen contractuel de retrouver la même intention plutôt que d’en créer une nouvelle par défaut.

## 5. Choisir ce que l’utilisateur doit attendre

La question utile n’est pas « peut-on tout rendre asynchrone ? », mais « quel résultat est nécessaire avant la prochaine décision de l’utilisateur ? ».

Pour notre cas :

| Interaction | Orientation à examiner |
|---|---|
| Vérifier la forme d’une demande | Retour immédiat lorsque possible |
| Consulter un fait déjà disponible | Réponse directe avec conditions de fraîcheur |
| Soumettre à un partenaire à durée incertaine | Prise en charge et suivi si le métier l’accepte |
| Informer après confirmation | Livraison différée avec reprise si elle n’est pas bloquante |
| Prendre une décision exigeant un fait certain | Obtenir ce fait ou limiter explicitement l’action |

Ce tableau est une proposition pour l’étude, pas une règle bancaire générale.

Un état d’attente n’est utile que s’il est compréhensible. Il doit distinguer traitement prévu, recherche en cours et résultat inconnu lorsque ces différences influencent l’action suivante.

Dans un cas fictif de déclaration de sinistre, un utilisateur peut obtenir immédiatement une preuve de réception sans attendre une décision d’instruction. Ce contrat doit encore préciser ce que la réception garantit et comment les compléments sont demandés.

## 6. Découpler dans le temps sans masquer la dépendance

Une file conservée permet à un producteur et un consommateur de ne pas être actifs exactement au même moment, dans les conditions de capacité, de durée de conservation et de disponibilité prévues.

Le pattern Queue-Based Load Leveling décrit une file servant de tampon pour lisser les pics et laisser le traitement progresser à un rythme contrôlé. Il rappelle aussi qu’un mécanisme de réponse supplémentaire est nécessaire si l’émetteur attend un résultat. [4]

La dépendance métier subsiste. Si le partenaire reste indisponible trop longtemps, le résultat reste absent. Si le broker refuse les publications ou si la capacité est atteinte, la prise en charge doit posséder un comportement défini.

Dans notre application, un stockage local durable peut conserver du travail avant un envoi ultérieur. Ce choix demande sa propre limite d’admission et sa reprise. Il ne permet pas d’accepter indéfiniment sans considérer la capacité restante.

Le découplage temporel est donc une propriété conditionnelle, pas une suppression de toutes les dépendances.

## 7. Comprendre les acquittements

Un système de messagerie distingue plusieurs étapes : publication, prise en charge par le broker, livraison au consommateur et traitement applicatif. Les confirmations techniques ne doivent pas être fusionnées avec le résultat métier.

RabbitMQ précise que les publisher confirms et les consumer acknowledgements sont indépendants : les premiers concernent l’interaction du producteur avec le broker ; les seconds concernent les livraisons au consommateur. Une confirmation de publication ne connaît pas le résultat métier de tous les consommateurs. [5]

| Observation | Ce qu’elle ne prouve pas seule |
|---|---|
| Octets écrits dans une connexion | Réception durable par le pair |
| Publication confirmée par le broker | Succès du traitement métier final |
| Message livré à un consommateur | Validation de sa transaction de données |
| Acquittement envoyé après traitement | Réception de cet acquittement par le broker |

La dernière situation peut conduire à une nouvelle livraison alors que l’effet local existe déjà. Le consommateur doit être conçu pour la répétition selon les garanties retenues. [5]

Il faut vérifier la configuration exacte du produit utilisé. Les termes, la durabilité et les modes d’acquittement ne doivent pas être extrapolés aveuglément d’un broker à un autre.

## 8. Mettre la charge et le retard dans le même raisonnement

Une file n’augmente pas, seule, la vitesse du système aval. Si les entrées dépassent durablement sa capacité de traitement, le retard augmente.

L’admission et le traitement doivent donc être conçus ensemble : débit autorisé, concurrence, taille des messages, coût des opérations et possibilités de rattrapage. Microsoft recommande de contrôler le rythme des consommateurs pour éviter de transmettre simplement les pics à la ressource suivante. [4]

Pour un calcul pédagogique, si une file contient N travaux et que la capacité effective dépasse le débit entrant de D travaux par seconde, le temps de résorption est approximativement N/D. Cette estimation suppose des débits stables, des travaux comparables, l’absence de nouvelles perturbations et la prise en compte des reprises dans la capacité effective.

Ce calcul n’est pas une mesure du système. Il montre pourquoi la profondeur seule ne suffit pas : il faut aussi connaître l’ancienneté et le rythme de progression.

Une solution doit définir ce qui se passe lorsque le retard dépasse la valeur utile pour le métier : limiter les nouvelles demandes, prioriser certains travaux ou orienter les cas devenus non traitables selon le contrat.

## 9. Définir délais, expiration et annulation

Le délai d’attente du client, l’échéance métier, la durée de conservation d’un message et le temps maximal d’une tentative sont des notions différentes.

Un client qui cesse d’attendre n’a pas nécessairement annulé le travail. Un message expiré dans une file ne constitue pas, à lui seul, une preuve que tous les effets déjà engagés ont été arrêtés.

Pour notre étude, une demande d’annulation doit être traitée comme une intention distincte, soumise aux règles et aux possibilités réelles. Si le partenaire a franchi une étape irréversible, la solution ne doit pas promettre un retour à la situation initiale.

La politique doit également préciser les travaux devenus trop anciens et la communication aux acteurs. Supprimer silencieusement une demande reçue peut contredire l’engagement de prise en charge.

Ces règles doivent rester explicites dans le modèle, même si leur réalisation utilise des timers ou des fonctions de broker.

## 10. Réessayer sans aggraver l’incident

Une tentative supplémentaire peut réussir après une perturbation transitoire. Elle peut aussi augmenter la charge d’un service déjà saturé ou répéter un effet dont la première réponse a été perdue.

AWS décrit ces deux risques et recommande de limiter les reprises, d’espacer les tentatives et d’utiliser du jitter pour éviter leur synchronisation. Le texte montre également l’amplification produite lorsque chaque niveau d’une chaîne effectue ses propres retries. [2]

Le choix doit donc préciser les erreurs réessayables, la sécurité de répétition, le budget total et l’endroit qui porte la politique.

Une validation métier refusée n’est pas automatiquement une erreur transitoire. Une indisponibilité ne justifie pas une attente infinie. Une sortie en erreur doit laisser une situation durable et compréhensible lorsque le travail a déjà été reçu.

Le chapitre consacré à la résilience approfondira ces mécanismes. Pour l’instant, leur présence rappelle que l’asynchronisme ne rend pas les retries gratuits.

## 11. Prévoir comment le résultat revient

Le client peut interroger un endpoint de suivi. Il peut aussi recevoir une notification ou un message de réponse, selon les capacités disponibles et le besoin.

Le polling doit avoir un rythme et une limite adaptés. Une population de clients qui interroge trop fréquemment peut déplacer la charge du traitement vers la consultation. Le pattern Microsoft prévoit notamment des indications de rythme de consultation dans la réponse. [1]

Une notification ne remplace pas toujours la source de consultation : elle peut être retardée ou sa réception ne pas être connue. Le contrat doit dire quelle représentation fait autorité pour le résultat affiché.

La corrélation doit distinguer identité métier, identité de message et référence d’échange. Réutiliser un identifiant pour plusieurs significations peut empêcher de comprendre si l’on observe une répétition ou une nouvelle tentative.

L’accès au suivi et aux réponses doit rester autorisé. Une référence d’opération est un moyen de retrouver une information, pas automatiquement un droit de la consulter.

## 12. Choisir avec une matrice de situations

| Besoin dominant | Choix à examiner | Coût à assumer |
|---|---|---|
| Information disponible et nécessaire immédiatement | Requête-réponse directe | Attente bornée et dépendance de disponibilité |
| Travail long avec résultat consultable | Prise en charge et suivi | État durable, corrélation et cycle de vie |
| Pic bref devant une capacité aval limitée | Tampon et traitement contrôlé | Retard, capacité et politique d’admission |
| Plusieurs réactions indépendantes à un fait | Publication vers consommateurs | Contrats, répétition et observation des suites |
| Exigence de certitude avant une action | Attendre le fait ou limiter le parcours | Refus explicite de certaines actions en mode dégradé |

Cette matrice propose des orientations, pas une sélection automatique d’outils.

La solution peut combiner les modes. Un appel synchrone reçoit la demande, le traitement est différé et une consultation synchrone expose ensuite le résultat. Le point important est que chaque étape ait un engagement distinct et vérifiable.

## 13. Exercice : le paiement prétendument terminé

Une API reçoit une demande, publie un message et affiche « paiement terminé » dès la confirmation du broker. Le consommateur peut rester arrêté plusieurs heures. Le client réessaie si la réponse HTTP n’arrive pas.

Identifier les engagements confondus et proposer une conception plus cohérente.

Une réponse argumentée distingue prise en charge, publication et résultat reconnu. Elle remplace l’affirmation finale par une représentation fidèle à ce qui est acquis et fournit un suivi autorisé.

Elle examine la conservation de l’intention avant réponse, la clé de reconnaissance, la capacité du tampon et l’ancienneté du travail. Elle prévoit les publications et livraisons répétées ainsi que les conditions d’une reprise.

Enfin, elle demande une preuve du contrat partenaire avant d’affirmer que toute nouvelle tentative est sûre. La confirmation de publication ne donne pas cette preuve.

## 14. Ouvrir la conception distribuée par les engagements

Synchrone et asynchrone ne désignent pas deux niveaux de modernité. Ils organisent différemment l’attente, le travail et la connaissance du résultat.

Une interaction directe reste adaptée lorsque le résultat est nécessaire et que la dépendance peut être assumée. Un traitement différé peut mieux gérer la durée ou la charge, mais exige un état, une corrélation et des reprises explicites.

Le reste du tome développera les mécanismes qui rendent ces engagements crédibles : sémantique des messages, événements, cohérence, idempotence, publication et consommation fiables. Le chapitre suivant commencera par distinguer commandes, requêtes, messages et événements afin que les échanges transportent un sens précis.

## Références

[1] Microsoft Learn, *Asynchronous Request-Reply pattern*, prise en charge, suivi et polling : https://learn.microsoft.com/en-us/azure/architecture/patterns/asynchronous-request-reply

[2] AWS Builders’ Library, *Timeouts, retries, and backoff with jitter*, attentes, effets répétés et amplification : https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/

[3] IETF, *RFC 9110 — HTTP Semantics*, section 15.3.3 sur 202 : https://www.rfc-editor.org/rfc/rfc9110.html

[4] Microsoft Learn, *Queue-Based Load Leveling pattern*, tampon, charge et rythme de traitement : https://learn.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling

[5] RabbitMQ, *Consumer Acknowledgements and Publisher Confirms*, garanties propres aux interactions AMQP décrites ; configuration et version à vérifier pour chaque réalisation : https://www.rabbitmq.com/docs/confirms

[Fin du tome II](../tome-02/chapitre-12.md) · [Sommaire](../SOMMAIRE.md)
