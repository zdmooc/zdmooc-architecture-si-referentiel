# Chapitre 1 — Délimiter une solution et ses responsabilités

## 1. Concevoir commence par une frontière

Le premier tome a établi les démarches et les moyens de raisonnement. Nous passons maintenant à la conception : comment transformer un besoin en responsabilités logicielles, en contrats et en mécanismes réalisables ?

Avant de choisir une architecture en couches ou des microservices, il faut savoir ce que la solution doit prendre en charge. Le périmètre ne se déduit pas du nom du projet. « Suivi des paiements » peut désigner un simple affichage, un processus de résolution ou la refonte d’une partie importante du SI.

Une frontière est un choix de conception. Elle précise les responsabilités assumées et les collaborations nécessaires, mais aussi ce qui reste hors du contrôle de l’équipe. Elle permet d’éviter deux erreurs opposées : promettre un résultat que la solution ne maîtrise pas, ou exclure une dépendance dont elle a pourtant besoin pour fonctionner.

Nous poursuivrons le cas fictif des paiements, puis examinerons une déclaration de sinistre pour distinguer les principes généraux des particularités de notre exemple principal.

## 2. Solution, produit, système et projet

Dans ce tome, une solution désigne l’ensemble cohérent de moyens retenus pour répondre à un besoin. Elle peut mobiliser plusieurs systèmes, des capacités communes et des interventions humaines.

Le produit constitue une perspective de valeur et d’évolution. Le système logiciel constitue une frontière de modélisation et de responsabilité. Le projet désigne une organisation temporaire du changement. Ces conventions pratiques ne doivent pas être transformées en identités automatiques.

Le modèle C4 définit le système logiciel comme une abstraction qui fournit de la valeur à ses utilisateurs et explique que sa frontière est souvent associée à une responsabilité d’équipe. Il précise également que capacités métier, domaines produit et contextes délimités ne doivent pas être assimilés automatiquement à des systèmes C4. [1]

Une solution peut donc traverser plusieurs systèmes. Un projet peut n’en modifier qu’une partie. Un produit peut continuer à évoluer après la fin du projet qui l’a créé.

## 3. Écrire l’engagement avant de dessiner les composants

Une formulation de périmètre doit expliquer ce que le service permet à un acteur d’accomplir et jusqu’où va son engagement.

Pour notre exercice, la solution permet à un client autorisé de consulter le dernier résultat connu d’un paiement et à un acteur habilité de traiter les situations nécessitant une recherche complémentaire. Elle ne prend pas en charge la réalisation interne du transfert chez le partenaire.

Cette formulation appelle immédiatement des questions. Le résultat connu provient de quelle autorité ? Qui est habilité à lancer une recherche ? Quelles opérations anciennes sont couvertes ? Le service expose-t-il seulement une information ou permet-il également une action ?

| Dimension | Décision à expliciter |
|---|---|
| Acteur | Qui bénéficie du service ou intervient ? |
| Intention | Quel résultat cherche-t-il ? |
| Engagement | Quel comportement la solution assure-t-elle ? |
| Limite | Qu’est-ce qui n’est pas promis ? |
| Dépendance | Qui fournit ce qui manque au périmètre ? |

Le tableau ne remplace pas le texte d’engagement. Il permet de vérifier que le nom du projet n’a pas masqué une question essentielle.

## 4. Représenter le contexte sans détailler prématurément

Une vue de contexte place le système étudié parmi les personnes et les systèmes avec lesquels il interagit directement. C4 recommande d’y privilégier les acteurs et les systèmes plutôt que les protocoles et détails techniques. [2]

Dans notre exemple, distinguons le service de suivi, le système qui possède les paiements, le partenaire et le fournisseur d’identité.

```text
[Client] ── consulte ──────────────────────────┐
                                              ▼
                                    [Système de suivi]
[Assistance] ── recherche et explique ─────────►│
                                              │
                ┌─────────────────────────────┼──────────────────┐
                ▼                             ▼                  ▼
      [Système Paiements]          [Partenaire externe]   [Identité]
      fournit les faits            répond selon contrat  établit l’identité
```

La séparation du suivi et de Paiements est ici une hypothèse de conception. Elle ne décide pas encore s’ils seront deux applications indépendantes ou des responsabilités dans un système plus large. La vue définit le périmètre que nous choisissons d’examiner.

Si l’étude porte sur toute la solution transverse, une vue de paysage peut compléter ces contextes. C4 distingue ce paysage d’un contexte centré sur un seul système. [3]

## 5. Inclure, exclure et traiter les cas limites

Écrire « hors périmètre » ne supprime pas une dépendance. Si la consultation exige une information détenue ailleurs, le contrat et la responsabilité de fourniture restent à établir.

Pour notre service, nous proposons :

| Inclus | Exclu | Cas limite à résoudre |
|---|---|---|
| Consultation d’un résultat connu | Exécution interne chez le partenaire | Paiement ancien sans référence exploitable |
| Recherche d’une situation incertaine | Modification libre du résultat | Résultat tardif incompatible |
| Contrôle du droit de consultation | Gestion générale des identités | Identité reconnue mais périmètre absent |
| Explication de la fraîcheur | Garantie d’une confirmation instantanée | Copie de consultation non actualisée |

Un cas limite indique une décision manquante, pas nécessairement une fonctionnalité à ajouter. La solution peut le refuser explicitement, l’orienter vers un autre processus ou l’intégrer avec des conditions précises.

Le périmètre initial doit couvrir un comportement cohérent. Réduire la première livraison n’autorise pas à laisser les erreurs importantes sans réponse.

## 6. Définir une responsabilité par ses décisions

Un nom comme « gestion des paiements » reste trop large pour guider la réalisation. Une responsabilité devient plus claire lorsqu’on précise ses décisions, les informations qu’elle utilise et les effets qu’elle peut produire.

Le système propriétaire du paiement peut décider d’une transition à partir de faits reconnus. Le suivi peut composer une réponse de consultation sans posséder l’autorité de modifier le résultat. L’intégration peut traduire un protocole sans décider seule du sens métier d’une confirmation.

```text
Paiements : établir et protéger l’état métier
Suivi : présenter les faits autorisés et leur fraîcheur
Intégration : adapter le contrat du partenaire
Résolution : organiser la recherche et son traitement
```

Cette répartition est une proposition à examiner avec les scénarios. Elle ne doit pas être conservée si elle impose une collaboration constante sans cohésion claire.

Une responsabilité ne doit pas devenir une classe géante. Elle peut être réalisée par plusieurs éléments internes, à préciser après avoir établi son contrat externe.

## 7. Identifier les autorités de données

La présence d’une donnée dans une base ne suffit pas à définir son autorité. Une copie peut servir à la lecture ; un historique peut expliquer une décision ; une référence peut permettre une recherche sans établir le résultat.

Pour notre exercice :

| Information | Autorité proposée | Usage du suivi |
|---|---|---|
| Résultat métier reconnu | Système Paiements | Présenter sans réinterprétation contradictoire |
| Résultat technique du partenaire | Contrat d’intégration et source externe | Alimenter une interprétation contrôlée |
| Droits de l’utilisateur | Autorités d’identité et d’habilitation retenues | Appliquer l’accès au périmètre |
| Vue de consultation | Responsabilité Suivi | Indiquer la fraîcheur et les limites |

Ces autorités peuvent être réalisées de différentes manières. Le tableau ne prescrit pas quatre bases ni quatre services.

Il faut également examiner les corrections. Si une information reconnue change, qui autorise ce changement, comment les copies sont-elles mises à jour et quels consommateurs doivent en être informés ?

La définition de l’autorité prépare les choix de cohérence. Elle ne les réalise pas encore.

## 8. Distinguer interface, contrat et transport

Une interface expose des opérations ou des messages. Le contrat en précise le sens, les conditions et les résultats. Le transport fournit un moyen de communication.

Deux applications peuvent échanger du JSON sur HTTP tout en utilisant des concepts incompatibles. Le succès du transport ne constitue pas la réussite du contrat métier.

Un contrat de consultation doit préciser l’identité du paiement, le périmètre d’accès, les résultats possibles et la fraîcheur. Un contrat de recherche doit distinguer absence de connaissance, absence de correspondance et rejet explicite de la demande.

Pour chaque échange, nous proposons de poser les questions suivantes dans le texte de conception : qui l’initie, à quelle fin, avec quelle identité, quelle réponse est attendue, quelles situations restent possibles et quel acteur les traite ?

C4 recommande de nommer les relations de manière cohérente avec le sens des flèches et d’expliquer les conventions. Un lien intitulé seulement « utilise » reste souvent trop pauvre pour faire apparaître ces engagements. [4]

## 9. Séparer les frontières qui se superposent parfois

La frontière fonctionnelle, la frontière de modèle, l’autorité des données, la frontière d’équipe et l’unité de déploiement ne sont pas nécessairement identiques.

Un module peut posséder des règles propres sans être déployé indépendamment. Deux applications peuvent être réalisées par la même équipe. Un partenaire peut appartenir au parcours métier sans relever de notre autorité d’exploitation.

Le guide C4 consacré aux microservices montre précisément que la représentation dépend aussi de la responsabilité exercée sur les services. Un microservice peut être représenté comme un groupe de conteneurs dans un système ou comme un système distinct selon le contexte. [5]

La décision doit donc expliciter les correspondances choisies. Dessiner plusieurs rectangles ne prouve ni l’autonomie ni l’isolation.

Lorsque le déploiement est étudié, il faut également nommer l’environnement. La représentation des applications et des stockages ne suffit pas à décrire leur redondance ou leur répartition de production. [6]

## 10. Tester le périmètre par un parcours

Une frontière convaincante doit permettre de raconter une situation complète. Commençons par un utilisateur qui consulte un résultat reconnu.

Le service établit le contexte d’accès, identifie le paiement, obtient les faits autorisés et retourne une présentation conforme au contrat. Si la donnée affichée provient d’une projection, sa fraîcheur doit avoir le sens annoncé.

Examinons ensuite une référence introuvable. Le service doit-il conclure que le paiement n’existe pas ? Pas nécessairement : l’absence peut provenir du périmètre de données ou de l’ancienneté couverte. Le contrat doit préciser ce que la réponse permet d’affirmer.

Enfin, examinons une recherche dont le traitement exige une intervention. Le périmètre n’est pas complet si la solution conserve la demande sans qu’aucun acteur ou mécanisme ne puisse la reprendre.

Ces parcours servent à vérifier les responsabilités. Ils ne remplacent pas les essais techniques nécessaires, mais révèlent ce qui doit être réalisé.

## 11. Prévoir les responsabilités d’exploitation

Une solution est aussi un service à soutenir. Ses frontières doivent préciser qui observe, qui intervient et qui décide lorsque le fonctionnement nominal n’est plus possible.

Dans notre cas, une copie de consultation peut être en retard alors que le système propriétaire des paiements fonctionne. Il faut pouvoir détecter ce décalage, expliquer ce que l’utilisateur voit et attribuer l’action de rétablissement.

| Situation | Responsabilité à attribuer |
|---|---|
| Échange externe indisponible | Détection, limitation des demandes et traitement du retard |
| Projection non actualisée | Mesure de fraîcheur et reprise de l’alimentation |
| Accès refusé de manière inattendue | Diagnostic sans contournement des droits |
| Recherche non résolue | Orientation et intervention habilitée |

Le tableau propose des responsabilités, pas une attribution universelle à une équipe plateforme ou applicative. Les mandats réels doivent être précisés.

Une frontière sans propriétaire opérationnel peut devenir un point mort entre équipes.

## 12. Vérifier le raisonnement avec un autre domaine

Prenons une déclaration de sinistre fictive. L’utilisateur veut savoir si les éléments transmis ont été reçus, si le dossier est complet et si une décision a été prise.

La réception d’une pièce ne signifie pas que le dossier est complet. Un dossier complet ne signifie pas que la demande est acceptée. La notification de décision ne constitue pas nécessairement un règlement. Ces distinctions sont des hypothèses pédagogiques à valider avec les règles du cas étudié.

Une solution de suivi peut afficher des faits provenant de plusieurs responsabilités sans être autorisée à produire elle-même la décision. Elle peut aussi organiser une demande de complément sans maîtriser le traitement de l’expertise.

La méthode reste la même : définir l’engagement, identifier les autorités, préciser les contrats et examiner les cas limites. Le modèle exact change avec le domaine ; le raisonnement ne dépend pas du vocabulaire du paiement.

## 13. Éviter trois découpages prématurés

Le premier consiste à découper selon les écrans. Deux parcours peuvent appliquer les mêmes règles ; les séparer entièrement risque de dupliquer la décision métier.

Le deuxième consiste à découper selon les tables. Une structure de stockage répond à des contraintes particulières et ne définit pas, seule, les responsabilités.

Le troisième consiste à découper selon les technologies. Une équipe qui possède le broker ne possède pas nécessairement le sens des événements transportés. Une base partagée ne justifie pas que tous ses consommateurs puissent modifier les mêmes informations.

Ces critères peuvent influencer la réalisation. Ils ne doivent pas remplacer l’analyse des intentions, des décisions et des autorités.

Le bon périmètre ne se mesure pas au nombre d’éléments. Il se reconnaît à la possibilité d’expliquer qui fait quoi et selon quel contrat, sans laisser de résultat important sans responsable.

## 14. Produire une description exploitable

À l’issue du travail, le lecteur doit pouvoir comprendre la solution sans assister à toutes les discussions. La description rassemble une promesse de service, une frontière, les acteurs, les responsabilités et les contrats significatifs.

Le contexte reste volontairement simple. Le niveau suivant ouvre la frontière pour représenter les applications et stockages et leur répartition de responsabilités. Dans C4, c’est le rôle du diagramme de conteneurs, distinct du déploiement. [6]

Les scénarios expliquent les collaborations. Les exigences précisent les comportements à vérifier. Les décisions conservent les alternatives et les raisons du découpage.

La cohérence entre ces éléments importe davantage que leur volume. Si une vue promet une recherche automatique mais qu’aucun composant ne la réalise, la description doit être corrigée.

## 15. Exercice : le suivi qui devient propriétaire de tout

Une équipe propose un nouveau service de suivi. Il posséderait une copie des données, modifierait directement les statuts, lancerait des recherches chez le partenaire et offrirait un bouton de correction aux agents. Le projet est présenté comme une simple amélioration de consultation.

Identifier les engagements réels et les responsabilités ajoutées. Proposer un périmètre initial cohérent et les contrats à établir.

Une réponse argumentée sépare présentation, décision métier, intégration et intervention. Elle demande pourquoi la copie pourrait modifier l’état autoritaire, quels droits encadrent la correction et qui traite les résultats contradictoires.

Un premier périmètre peut se limiter à une consultation autorisée et à l’orientation explicite des situations non résolues. Un autre peut inclure la résolution, mais doit alors en assumer les données, règles et conditions d’exploitation. Le bon choix dépend du besoin, pas du nom « suivi ».

L’exercice ne conduit pas automatiquement à plusieurs microservices. Il établit d’abord les responsabilités qui devront être réalisées.

## 16. Une frontière rend la collaboration possible

Délimiter une solution ne consiste pas à l’isoler artificiellement du reste du SI. Cela consiste à rendre ses engagements et ses dépendances compréhensibles.

Une frontière utile explique la valeur fournie, les décisions prises, les données maîtrisées et les situations confiées à d’autres acteurs. Elle prépare le choix des modèles et des mécanismes sans le remplacer.

Le chapitre suivant approfondira cette question avec le DDD stratégique : comment construire un langage, distinguer les sous-domaines et organiser des contextes dont les modèles restent cohérents ?

## Références

[1] C4 model, *Software system*, abstraction et frontières de responsabilité : https://c4model.com/abstractions/software-system

[2] C4 model, *System context diagram*, acteurs et dépendances externes : https://c4model.com/diagrams/system-context

[3] C4 model, *System landscape diagram*, représentation d’un ensemble de systèmes : https://c4model.com/diagrams/system-landscape

[4] C4 model, *Notation*, libellés, types et conventions de représentation : https://c4model.com/diagrams/notation

[5] C4 model, *Microservices*, représentation selon les responsabilités et frontières retenues : https://c4model.com/abstractions/microservices

[6] C4 model, *Container diagram*, responsabilités internes et distinction avec le déploiement : https://c4model.com/diagrams/container

[Fin du tome I](../tome-01/chapitre-12.md) · [Sommaire](../SOMMAIRE.md)
