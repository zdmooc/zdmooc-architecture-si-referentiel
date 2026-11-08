# Chapitre 11 — ArchiMate, UML et BPMN : représenter pour comprendre

## 1. Un langage n’est pas une méthode d’architecture

Une équipe peut employer TOGAF pour organiser une étude, DDD pour construire un modèle métier et un langage graphique pour communiquer ses décisions. Ces contributions ne sont pas interchangeables. Un langage définit des concepts, des relations et des règles d’interprétation ; il ne décide pas à la place de l’architecte.

ArchiMate sert à décrire et à analyser les architectures d’entreprise et leurs relations. UML propose un langage de modélisation des structures et des comportements des systèmes logiciels. BPMN vise la représentation des processus et des collaborations. [1][2][3]

Le choix ne se résume donc pas à une préférence graphique. Il dépend de la question, du public, du niveau de détail et des analyses attendues. Il faut également pouvoir expliquer ce que le dessin ne montre pas.

Dans ce chapitre, nous suivrons le même service fictif de paiements. Les schémas textuels illustrent le raisonnement ; ils ne prétendent pas reproduire toutes les conventions graphiques des langages normalisés.

## 2. Commencer par la question à résoudre

Le chapitre 2 a distingué vue et point de vue. Nous ajoutons maintenant les moyens de représentation adaptés à certaines de ces vues.

La direction veut comprendre quelles activités dépendent d’une application. L’équipe de conception veut examiner une transition d’état. Le métier veut savoir ce qui arrive lorsqu’une réponse tarde. Ces questions peuvent concerner le même système sans appeler le même diagramme.

| Question | Représentation à examiner |
|---|---|
| Quels services applicatifs soutiennent une activité ? | Vue ArchiMate reliant les domaines |
| Quels concepts et associations structurent le modèle ? | Diagramme de classes UML |
| Quels échanges composent un scénario ? | Diagramme de séquence UML |
| Quelles transitions sont autorisées ? | Machine à états UML |
| Comment répartir le processus et ses exceptions ? | Processus ou collaboration BPMN |
| Où les artefacts sont-ils installés ? | Diagramme de déploiement UML |

Cette table propose une orientation, pas une exclusivité. Un langage peut couvrir plusieurs besoins. Une convention simple peut également suffire lorsqu’aucune analyse normalisée n’est nécessaire.

## 3. Le modèle et ses représentations

Un diagramme sélectionne des éléments d’un modèle. Deux dessins peuvent présenter le même composant depuis des perspectives différentes sans créer deux composants distincts.

ArchiMate exprime le modèle comme un ensemble de concepts, éléments ou relations. Sa structure permet de représenter plusieurs domaines et leurs liens. [1][4]

Dans notre étude, l’application de suivi peut apparaître dans une vue d’ensemble avec les activités métier, puis dans une vue technique avec ses dépendances. Les deux représentations doivent garder une correspondance claire.

Cette discipline importe aussi lorsque plusieurs langages sont utilisés. Le service de consultation décrit à l’échelle d’entreprise ne correspond pas nécessairement à une classe UML unique. Il faut préciser comment il est réalisé plutôt que supposer une identité de représentation.

Un outil peut aider à conserver ces liens, mais il ne corrige pas une modélisation dont les responsabilités sont mal définies.

## 4. ArchiMate : relier métier, applications et technologie

Le cœur d’ArchiMate distingue les couches métier, application et technologie. Il permet notamment d’exprimer les services, les comportements, les structures qui les portent et les informations concernées. Le langage comprend également des concepts pour la stratégie, la motivation et la transformation. [1][4][5]

Pour le cas fictif, nous voulons relier la capacité de suivi à sa réalisation. Une lecture pédagogique peut être :

```text
Activité métier : expliquer le résultat connu
           ↑ bénéficie de
Service applicatif : consulter le statut
           ↑ réalisé par des comportements de
Application de paiements
           ↑ utilise
Services technologiques d’exécution et de stockage
```

Le schéma exprime une intention de lecture. Pour en faire une vue ArchiMate formelle, il faut choisir les types exacts et les relations autorisées entre eux.

L’intérêt est de rendre la dépendance intelligible. Si un service de stockage change, quelles applications et quelles activités peuvent être affectées ? La vue ne démontre pas automatiquement la gravité de l’impact ; elle fournit les relations à analyser.

## 5. Respecter le sens des relations ArchiMate

Les flèches ne sont pas décoratives. Une relation de service rendu, une réalisation et un flux d’information expriment des significations différentes.

Dans le vocabulaire de la famille ArchiMate 3 étudiée ici, Serving exprime notamment qu’une fonctionnalité sert un autre élément. Realization relie une réalisation à une abstraction. Les liens entre couches utilisent ces distinctions pour rendre les dépendances lisibles. [4][6]

Il faut vérifier l’orientation et les couples de types autorisés. Une relation compréhensible en langage naturel n’est pas nécessairement valide entre deux éléments précis du métamodèle.

Le guide communautaire ArchiMate 101 insiste sur cette contrainte : toutes les relations ne sont pas admises entre tous les éléments. Il signale également que l’imbrication graphique peut représenter certaines relations, à condition de respecter les conventions. [5]

Un grand rectangle autour de plusieurs applications ne signifie donc pas automatiquement propriété, déploiement et composition à la fois. Si le dessin repose sur une convention locale, elle doit être expliquée.

## 6. Ne pas réduire ArchiMate à un inventaire technique

Une vue remplie de composants et de nœuds peut rester muette sur les objectifs et les règles. ArchiMate permet une lecture plus large, notamment par les concepts de motivation, de stratégie et de transformation. [5]

Dans notre cas, l’objectif est de rendre le suivi compréhensible. Une exigence peut imposer de distinguer réception et exécution. La vue doit montrer quelles responsabilités contribuent à cette exigence, sans prétendre que la présence d’un lien prouve leur réalisation correcte.

Il faut également préciser le statut de la représentation : existant, cible ou transition. Les mélanger sans distinction peut faire croire qu’un contrôle ou qu’une interface existe déjà.

La bonne profondeur dépend du public. Une vue destinée à arbitrer la trajectoire n’a pas besoin de toutes les classes. Une vue destinée à comprendre un incident ne peut pas toujours rester au niveau des capacités métier.

## 7. UML : un ensemble de modèles structurels et comportementaux

UML, Unified Modeling Language, définit un langage de modélisation pour visualiser, spécifier et documenter des systèmes. Sa spécification comprend notamment les classes, composants, déploiements, activités, interactions et machines à états. [2][7]

Le langage ne se limite pas aux diagrammes de classes. Une conception qui décrit seulement les objets peut ignorer les échanges et les règles temporelles essentiels au comportement.

Dans notre application, trois vues sont particulièrement utiles : les concepts du domaine, le scénario de réponse perdue et les transitions permises. Elles doivent être reliées, mais ne montrent pas la même chose.

UML ne constitue pas une preuve automatique de conformité du code au modèle. Pour établir cette correspondance, il faut des revues, des tests ou des mécanismes de contrôle appropriés.

## 8. Les classes : identité, associations et multiplicité

Un diagramme de classes exprime des types, des propriétés, des opérations et des relations. Les multiplicités précisent notamment combien de valeurs ou d’objets peuvent être associés dans le modèle retenu. [7]

Pour notre exercice, un paiement possède une identité et peut donner lieu à plusieurs tentatives d’échange. La tentative technique ne doit pas être confondue avec une nouvelle intention de paiement.

```text
Paiement
  identifiant
  montant
  connaissanceDuResultat
  confirmerExecution(...)
        │
        │ est associé à 0..* tentatives
        ▼
TentativeEchange
  referenceTechnique
  instantEnvoi
  resultatTechniqueConnu
```

Cette illustration ne définit ni un schéma de tables ni une frontière d’agrégat. Une association UML ne signifie pas automatiquement que tous les objets doivent être chargés et modifiés dans une transaction unique.

Il faut également utiliser composition et héritage pour leur sens, pas pour remplir le dessin. Une structure de stockage ou un besoin de réutilisation de code ne justifie pas, à lui seul, une relation métier donnée.

## 9. Les séquences : faire apparaître les échanges et leurs variantes

Un diagramme de séquence représente une interaction entre des lignes de vie, avec des messages et un ordre d’occurrences. UML propose des fragments pour exprimer notamment alternatives, répétitions et interactions parallèles. [7]

Dans notre cas, le scénario doit distinguer la demande envoyée, le traitement distant et la connaissance locale du résultat.

```text
Application        Stockage        Partenaire
    │                  │                │
    ├─ conserver référence ─►           │
    │                  │                │
    ├─ soumettre avec référence ────────►│
    │                  │                │ traite la demande
    │   réponse non reçue               │
    │                  │                │
    ├─ conserver l’incertitude ─►       │
    │                  │                │
    ├─ rechercher le résultat ──────────►│
    │◄──────── résultat reconnu ────────┤
```

Le dessin est textuel et simplifié. Une version UML formelle préciserait les messages, les alternatives et les contraintes nécessaires.

Une flèche descendante n’établit pas une durée. Un ordre représenté n’impose pas automatiquement un ordre global entre tous les participants. Une transaction ne doit pas être supposée couvrir un appel distant simplement parce que celui-ci figure entre deux messages locaux.

La séquence aide à poser ces questions ; les contrats et les mécanismes doivent apporter les réponses.

## 10. Les machines à états : préciser ce qui peut changer

Une machine à états UML décrit des états et des transitions, avec des événements déclencheurs et, lorsque nécessaire, des gardes et des effets. [7]

Dans le cas fictif, nous pouvons étudier uniquement la connaissance du résultat, indépendamment du cycle complet de prise en charge :

```text
Résultat non établi
    ├─ confirmation reconnue → Exécution confirmée
    └─ rejet reconnu ────────→ Rejet établi
```

Une confirmation reconnue signifie ici que les conditions du contrat ont été vérifiées. Elle ne correspond pas à toute réponse HTTP réussie.

Une garde exprime une condition de transition. Un effet exprime un comportement associé. Les confondre peut conduire à traiter une action comme si elle constituait sa propre autorisation.

Il faut aussi prévoir les entrées inattendues. Une confirmation répétée, un rejet tardif contradictoire ou une demande de correction appellent un comportement explicite. Ils ne doivent pas écraser silencieusement un état reconnu comme définitif dans le modèle.

## 11. Composants et déploiement : séparer logiciel et instances

Un modèle de composants aide à représenter des responsabilités logicielles et leurs interfaces. Un modèle de déploiement traite notamment les artefacts, les nœuds et leur installation. [7]

Une application peut être représentée par ses composants tout en étant déployée comme une seule unité. Plusieurs instances de cette unité ne créent pas plusieurs modèles métier.

Pour notre cas, il faut distinguer le composant de consultation, le binaire qui le contient et les instances de ce binaire dans un environnement. L’instance de production peut utiliser un stockage et un réseau différents de ceux des essais.

Le langage permet de décrire cette distribution. Il ne démontre pas que deux instances sont réellement indépendantes ni que le stockage partagé résiste au scénario de panne considéré. La vue doit être complétée par les exigences et les preuves.

## 12. BPMN : représenter le processus et la collaboration

BPMN, Business Process Model and Notation, vise à représenter les processus avec un langage compréhensible par les acteurs qui les conçoivent, les gèrent et les réalisent. La notation est indépendante d’un environnement d’implémentation particulier. [3]

Les éléments comprennent les activités, les événements, les passerelles, les flux, les participants et les couloirs. Un processus montre l’enchaînement d’un participant ; une collaboration permet de représenter les échanges entre participants. [8]

Dans notre cas, l’organisation de paiements et le partenaire peuvent être représentés comme deux participants distincts. L’assistance peut figurer dans un couloir du processus interne si le périmètre choisi l’exige.

```text
Participant : organisation
  Recevoir → Contrôler → Soumettre → Attendre le résultat
                                  │
                                  │ message
                                  ▼
Participant : partenaire
  Recevoir la demande → Traiter → Communiquer le résultat
```

Ce schéma textuel n’utilise pas toute la notation BPMN. Il introduit la séparation entre déroulement interne et échange entre participants.

## 13. Pools, lanes et flux : éviter les erreurs de frontière

Un pool représente un participant dans une collaboration. Une lane organise des activités à l’intérieur d’un processus. Les flux de séquence décrivent le déroulement interne, tandis que les flux de message relient des participants distincts. Les règles BPMN interdisent de faire traverser une frontière de pool à un flux de séquence. [8]

Deux services internes ne doivent pas être placés automatiquement dans deux pools sous prétexte qu’ils communiquent par HTTP. La modélisation dépend du sens des participants et de la collaboration étudiée, pas seulement du protocole technique.

Inversement, relier l’organisation et un partenaire externe par une séquence unique peut laisser croire à un contrôle commun de leur déroulement. Un pool externe peut rester fermé lorsque son fonctionnement interne n’est pas connu ou n’est pas pertinent.

Un couloir attribue une organisation du travail dans le modèle. Il ne constitue pas un contrôle d’autorisation exécuté. Si une tâche sensible est réservée à un acteur, la réalisation doit encore appliquer et vérifier cette règle.

## 14. Passerelles et événements : exprimer les situations sans les confondre

Une passerelle exclusive fondée sur les données sélectionne une branche selon les conditions. Une passerelle parallèle exprime une séparation ou une synchronisation de chemins concurrents. Une passerelle fondée sur les événements permet de représenter un choix déterminé par l’événement qui survient. [8]

L’absence de réponse dans notre cas peut conduire à examiner une attente de message et une échéance. Il ne faut pas transformer un délai dépassé en rejet métier uniquement pour fermer le processus.

```text
Après soumission
    ├─ résultat reconnu reçu → Enregistrer le résultat
    └─ échéance atteinte ───→ Organiser la recherche du résultat
```

Cette illustration exprime des alternatives de situation. Une réalisation BPMN formelle doit préciser le mécanisme retenu : attente événementielle, événements attachés ou autre structure adaptée, avec sa sémantique exacte.

Une synchronisation parallèle mal placée peut attendre un chemin qui ne sera jamais activé. Une activité interrompue dans un moteur local n’implique pas que le partenaire ait annulé son action. Le sens de l’interruption doit donc rester distinct des effets distants.

## 15. Un processus dessiné n’est pas nécessairement exécutable

La spécification BPMN distingue la modélisation des processus et leur exécution, avec des points de conformité différents. [8]

Un processus destiné à la compréhension métier peut omettre des informations nécessaires à un moteur : expressions, données, corrélations, intégrations et détails d’erreur. La présence d’un fichier compatible avec un outil ne prouve pas la portabilité complète de son exécution.

Dans notre exemple, une tâche « rechercher le résultat » doit devenir une opération réalisable : référence, contrat, droits, erreurs et reprises. Dessiner un symbole de compensation ne produit pas automatiquement une correction métier sûre.

Le moteur peut coordonner un déroulement ; il ne remplace pas les invariants du domaine ni les garanties d’idempotence des opérations appelées. La cohérence doit être conçue entre processus et applications.

## 16. Articuler les trois langages sur un même cas

Une vue ArchiMate peut relier l’activité de résolution aux services et applications nécessaires. Un processus BPMN décrit la collaboration qui permet de résoudre la situation. Une séquence UML détaille un échange critique. Une machine à états UML définit les changements légitimes de la connaissance du résultat.

| Élément du cas | Correspondances à maintenir |
|---|---|
| Résoudre l’incertitude | Activité, responsabilité applicative et processus |
| Demander le statut partenaire | Tâche, opération d’interface et messages de séquence |
| Confirmer l’exécution | Fait reconnu, transition et résultat du processus |
| Exécuter la reprise | Acteur habilité, composant et instance opérationnelle |

Il n’existe pas de traduction automatique de chaque élément vers un élément unique d’un autre langage. Une tâche métier peut mobiliser plusieurs opérations ; une opération peut soutenir plusieurs processus.

Les noms et les contrats doivent permettre de suivre ces correspondances. Si le BPMN affirme que la confirmation est vérifiée mais qu’aucune responsabilité applicative ne le fait, les modèles ne décrivent pas une solution cohérente.

Les travaux de The Open Group sur ArchiMate soulignent également l’articulation avec les modèles détaillés en UML et BPMN. [9]

## 17. Qualifier la représentation sans surestimer sa preuve

Trois questions sont utiles. La représentation respecte-t-elle les conventions choisies ? Décrit-elle correctement le besoin ou le système ? La réalisation se comporte-t-elle comme annoncé ?

Un contrôle de syntaxe peut répondre à une partie de la première question. Une revue métier et technique contribue à la deuxième. Les essais, mesures et observations de réalisation contribuent à la troisième.

Il faut éviter de faire porter à l’outil une garantie qu’il ne fournit pas. Une relation autorisée peut exprimer une hypothèse fausse. Un diagramme de séquence bien formé peut oublier une panne. Une machine à états cohérente peut être contournée par une API mal protégée.

La lisibilité compte aussi. Un modèle exact mais illisible pour son public échoue à communiquer. Une vue plus limitée, accompagnée d’un scénario clair, peut mieux soutenir une décision qu’un dessin exhaustif.

## 18. Exercice : choisir les modèles nécessaires

L’organisation veut retirer un ancien outil d’assistance. Le nouvel écran affiche le statut, mais les recherches de résultats tardifs sont encore réalisées manuellement. Les équipes ne savent pas quelles applications utilisent l’ancienne référence.

Proposer une combinaison de représentations pour examiner le retrait. Expliquer ce que chacune permet de vérifier et ce qui exige une preuve supplémentaire.

Une réponse argumentée utilise une vue d’ensemble pour repérer les dépendances de l’outil et de ses services. Une collaboration BPMN explicite la recherche manuelle et le partenaire. Une séquence détaille l’interrogation avec la référence ancienne. Une machine à états vérifie comment le résultat tardif modifie la connaissance locale.

Les modèles ne suffisent pas à autoriser le retrait. Il faut vérifier les usages réels, les données accessibles, les contrats et les scénarios de reprise. L’absence d’une flèche dans le modèle n’est pas une preuve d’absence d’un consommateur réel.

## 19. Représenter pour décider, pas pour décorer

ArchiMate, UML et BPMN apportent des vocabulaires complémentaires. Leur intérêt est de rendre les relations, les comportements et les responsabilités moins ambigus.

La discipline de modélisation commence par une question et se poursuit par le choix des éléments, des relations et du niveau de détail. Elle exige ensuite de maintenir les correspondances avec les autres vues et avec le système réel.

Dans notre cas des paiements, les modèles doivent aider à expliquer un résultat, une attente, une reprise et une dépendance. Le chapitre suivant conclura ce premier tome par la gouvernance des décisions, des exceptions et de la dette : les représentations servent à raisonner, mais l’organisation doit encore arbitrer et faire évoluer ses choix.

## Références

[1] The Open Group, *ArchiMate — Introduction*, langage, domaines et relations ; introduction de la famille 3, sans affirmation qu’il s’agit de la dernière édition : https://pubs.opengroup.org/architecture/archimate3-doc/index.html

[2] Object Management Group, *Unified Modeling Language 2.5.1*, présentation du langage : https://www.omg.org/spec/UML/2.5.1

[3] Object Management Group, *Business Process Model and Notation 2.0.2*, portée et notation : https://www.omg.org/spec/BPMN/2.0.2

[4] The Open Group, *ArchiMate 3.0 — Language Structure*, concepts et couches : https://pubs.opengroup.org/architecture/archimate30-doc/chap03.html

[5] The Open Group, communauté ArchiMate, *ArchiMate 101: A Practical Introduction*, guide pédagogique distinct du texte normatif : https://archimate-community.pages.opengroup.org/workgroups/archimate-101/

[6] The Open Group, *ArchiMate 3.0.1 — Cross-Layer Dependencies*, Serving et Realization entre couches : https://pubs.opengroup.org/architecture/archimate301-doc/chap12.html

[7] Object Management Group, *UML 2.5.1 — Specification*, classes, composants, déploiements, interactions et machines à états : https://www.omg.org/spec/UML/2.5.1/PDF

[8] Object Management Group, *BPMN 2.0.2 — Specification*, clauses 2, 7, 9, 10 et 13 sur conformité, participants, flux et exécution : https://www.omg.org/spec/BPMN/2.0.2/PDF

[9] The Open Group, *ArchiMate: Adding value to TOGAF*, présentation historique sur l’articulation des modèles d’ensemble et détaillés : https://archive.opengroup.org/public/member/proceedings/q110/archimate.pdf

[Chapitre précédent](chapitre-10.md) · [Sommaire](../SOMMAIRE.md)
