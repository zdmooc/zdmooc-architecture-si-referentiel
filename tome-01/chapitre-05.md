# Chapitre 5 — L’architecture d’entreprise : capacités, existant et cible

## 1. Dépasser le périmètre d’une application

Une application de paiements peut être améliorée sans que le service rendu par l’organisation progresse réellement. Une nouvelle interface peut afficher rapidement un état qui reste ambigu. Un traitement peut être plus fiable alors que l’assistance continue à ressaisir des informations entre plusieurs outils. Une plateforme peut être renouvelée tandis que les règles de responsabilité restent contradictoires.

Ces situations montrent la limite d’une lecture centrée sur un seul logiciel. L’architecture d’entreprise examine les relations entre les objectifs, les activités, les informations, les applications, les technologies et les responsabilités. Elle cherche à rendre la transformation cohérente au-delà du projet local.

L’ambition n’est pas de dessiner tout le système d’information avant d’autoriser un changement. Elle est d’identifier les dépendances significatives pour la décision. Un périmètre limité peut être étudié en profondeur sans prétendre décrire exhaustivement l’organisation.

Dans ce chapitre, nous élargissons notre cas fictif : améliorer le suivi des paiements ne concerne plus seulement l’application principale, mais aussi les canaux clients, le partenaire d’exécution, l’assistance et les outils de traitement des situations incertaines.

## 2. Partir du résultat recherché

Un objectif de transformation doit exprimer ce que l’organisation souhaite obtenir. « Remplacer la plateforme » décrit une action. « Réduire les situations où le client ne peut pas comprendre le résultat d’une demande » décrit un résultat qui peut justifier plusieurs actions.

Le résultat doit être précisé. S’agit-il de réduire le nombre d’opérations sans statut expliqué, le délai de clarification ou l’effort d’assistance ? Ces dimensions peuvent se compléter sans être identiques. L’amélioration de l’une ne démontre pas automatiquement celle des autres.

Une présentation de l’architecture métier publiée par The Open Group illustre ce passage de la vision aux besoins et aux écarts, avant la définition de solutions comprenant processus, rôles, systèmes et flux d’information. [1]

Dans notre cas, la question devient donc : de quelles aptitudes l’organisation a-t-elle besoin pour fournir un suivi compréhensible et pour résoudre les exceptions ? Cette formulation introduit les capacités métier.

## 3. Comprendre la capacité métier

Une capacité métier décrit ce que l’organisation doit être capable d’accomplir. Elle ne prescrit pas immédiatement l’équipe, le logiciel ou la technologie qui le réalisera.

Dans notre exemple, « suivre une demande de paiement » ne désigne ni un écran ni une table. Cette capacité peut mobiliser la collecte des résultats, leur interprétation, leur exposition et le traitement des cas incertains. Sa réalisation combine donc des personnes, des processus, des informations et des systèmes.

Les publications de The Open Group rapprochent explicitement les capacités de ce que l’organisation sait faire et les chaînes de valeur des mécanismes par lesquels elle produit de la valeur pour les parties prenantes. [1][2]

| Notion | Ce qu’elle décrit | Exemple fictif |
|---|---|---|
| Objectif | Résultat recherché | Réduire le délai de clarification |
| Capacité | Aptitude nécessaire | Résoudre une situation de paiement incertaine |
| Processus | Enchaînement d’activités | Rechercher, rapprocher puis expliquer le résultat |
| Organisation | Répartition du travail | Assistance et équipe de traitement |
| Application | Support logiciel | Outil de suivi et de rapprochement |
| Technologie | Moyen de réalisation | Runtime, stockage et réseau |

La capacité fournit un point d’ancrage lorsque les équipes ou les applications changent. Elle n’est pas immuable : une transformation du métier peut modifier les capacités requises et leur découpage.

## 4. Construire une carte utile

Une carte de capacités organise les aptitudes à un niveau suffisamment stable pour discuter des investissements. Elle ne doit être ni une copie de l’organigramme ni une liste de produits.

Pour notre étude, nous proposons une carte réduite :

```text
Gestion des paiements
├── Recevoir les demandes
├── Évaluer leur admissibilité
├── Piloter leur exécution
├── Suivre les résultats
├── Résoudre les situations incertaines
└── Informer les utilisateurs
```

Ce découpage est pédagogique. Il ne prétend pas reproduire un standard bancaire. Sa qualité doit être jugée à partir des décisions qu’il permet de prendre.

Les libellés doivent être compréhensibles sans référence à une application. « Gestion de la table PAIEMENT » serait un libellé technique. « Support de l’équipe X » dépendrait d’un organigramme. « Résoudre les situations incertaines » exprime une aptitude qui peut rester nécessaire malgré un changement d’outil ou de responsabilité opérationnelle.

Il faut aussi préciser le niveau de décomposition. Mélanger « gérer les paiements » et « vérifier la présence d’un champ » au même niveau rend la carte difficile à exploiter.

## 5. Relier les capacités à la valeur

Une capacité peut être bien réalisée sans contribuer de la même manière à tous les parcours. Une chaîne de valeur aide à examiner les résultats successifs obtenus pour une partie prenante.

The Open Group distingue les chaînes de valeur, orientées vers la valeur créée, des processus métier qui détaillent la manière de réaliser les activités. Des capacités peuvent soutenir les étapes de la chaîne, elles-mêmes réalisées par des processus. [2]

Pour un client de notre organisation fictive, nous pouvons retenir :

```text
Demande comprise
      ↓
Prise en charge connue
      ↓
Résultat établi ou attente expliquée
      ↓
Situation résolue
```

Ces étapes expriment une lecture de la valeur, pas un protocole informatique. La capacité de rapprochement peut soutenir la résolution d’une situation sans être visible dans le parcours nominal. Une capacité peu sollicitée peut ainsi être essentielle lorsque le service rencontre une exception.

La correspondance entre capacités et valeur empêche de financer uniquement les éléments visibles. Un écran renouvelé ne suffit pas si les mécanismes qui établissent le résultat restent défaillants.

## 6. Décrire l’existant sans le réinventer

L’architecture existante représente le système tel qu’il fonctionne dans le périmètre étudié. Elle ne doit pas être reconstruite à partir de ce que les documents anciens affirment qu’il devrait faire.

L’étude doit confronter plusieurs éléments : entretiens, contrats d’interface, configurations, observations de flux, procédures, incidents et mesures. Il faut distinguer fait observé, affirmation documentée et hypothèse non vérifiée.

Dans notre cas, la documentation peut mentionner un rapprochement automatique alors que certaines situations passent réellement par un échange manuel avec le partenaire. Cette différence modifie l’évaluation de la capacité. L’automatisation annoncée ne correspond pas entièrement au service effectif.

L’inventaire applicatif est nécessaire, mais insuffisant. Il faut également découvrir les fichiers échangés, les traitements planifiés, les copies locales, les opérations manuelles et les connaissances détenues par quelques personnes.

Une description utile relie les éléments à leur rôle. Savoir qu’un serveur existe n’explique pas pourquoi sa suppression empêcherait une procédure de clôture ou une recherche d’opération ancienne.

## 7. Croiser capacités, applications et données

Une correspondance entre capacités et applications révèle comment le patrimoine soutient le métier. Elle peut faire apparaître plusieurs outils pour une capacité ou une application couvrant plusieurs capacités.

Aucune de ces situations n’est automatiquement un défaut. Deux applications peuvent servir des populations ou des règles différentes. Une application intégrée peut fournir un ensemble cohérent. Le croisement sert à poser des questions, pas à imposer un rapport d’un pour un.

| Capacité du cas fictif | Réalisation existante supposée | Difficulté à vérifier |
|---|---|---|
| Recevoir les demandes | Canal client et application principale | Sens de l’accusé de réception |
| Suivre les résultats | Application et outil d’assistance | États et fraîcheur différents |
| Résoudre l’incertitude | Traitement manuel et interrogation partenaire | Références parfois manquantes |
| Informer l’utilisateur | Canal client et notifications | Messages non alignés avec les états |

Cette matrice ne vaut pas constat d’audit : les éléments sont les hypothèses de notre exemple. Dans une étude réelle, ils doivent être vérifiés.

Le croisement doit être prolongé par les données. Si deux applications présentent un résultat, laquelle maîtrise sa signification ? La seconde possède-t-elle une copie, une projection ou une autorité différente ? Une consolidation technique qui ignore cette question peut déplacer l’ambiguïté au lieu de la résoudre.

## 8. Évaluer sans fabriquer une précision artificielle

Une carte colorée peut donner l’impression d’une analyse achevée. Pourtant, un indicateur rouge n’explique ni la cause du problème ni l’action nécessaire.

Pour notre étude, nous proposons d’examiner séparément l’importance pour l’objectif, les difficultés constatées, les risques, le coût de fonctionnement et la capacité de changement. Chaque appréciation doit préciser ses éléments d’appui.

Une capacité peut être correctement réalisée mais coûteuse à modifier. Une autre peut être peu automatisée tout en répondant au besoin actuel. Une troisième peut apparaître robuste en fonctionnement normal et dépendre, en incident, d’une personne difficilement remplaçable.

Additionner ces dimensions dans une note unique risque de masquer le raisonnement. Il est souvent plus utile d’expliquer que le traitement des résultats inconnus souffre d’un manque de références et d’une responsabilité imprécise que de lui attribuer seulement un score de maturité.

L’incertitude doit rester visible. Un domaine mal connu n’est pas nécessairement un domaine défaillant ; il nécessite d’abord un effort de connaissance.

## 9. Définir une cible de fonctionnement

La cible décrit l’état souhaité pour répondre aux objectifs et aux contraintes. Elle ne se limite pas à une liste de composants nouveaux.

Dans notre cas, la cible pourrait établir qu’un même sens des états est appliqué dans les canaux clients et l’assistance, que les situations incertaines sont identifiables, que les références nécessaires sont conservées et que leur résolution possède un responsable.

```text
Objectif : rendre le suivi compréhensible
             │
             ├── Modèle d’états partagé dans le périmètre
             ├── Référence permettant le rapprochement
             ├── Consultation avec fraîcheur expliquée
             └── Traitement des exceptions attribué
```

Ces principes de fonctionnement orientent les solutions sans imposer immédiatement leur technologie. Une application modulaire, des services séparés ou l’adaptation d’un outil existant restent des options à examiner.

La cible doit aussi préciser ses limites. Si le partenaire ne peut pas fournir un résultat immédiat, elle ne doit pas promettre une certitude instantanée. Elle peut prévoir une attente explicitée et une résolution suivie, sous réserve d’acceptation par le métier.

## 10. Comparer existant et cible

L’analyse des écarts examine ce qui doit être conservé, créé, modifié ou retiré pour passer de l’existant à la cible. Les techniques TOGAF historiques décrivent notamment une comparaison entre architecture de référence existante et architecture cible, avec examen des éléments nouveaux ou éliminés. [3]

| Existant de l’exemple | Cible proposée | Écart à traiter |
|---|---|---|
| États interprétés différemment | Signification commune dans le périmètre | Clarifier et traduire les contrats |
| Références incomplètes | Référence durable et exploitable | Modifier capture, stockage et contrôles |
| Recherche manuelle dispersée | Traitement de rapprochement attribué | Outiller et organiser la résolution |
| Copie ancienne non signalée | Fraîcheur connue lors de la consultation | Adapter la circulation et l’affichage |

Un écart n’est pas encore un projet. Plusieurs écarts peuvent être traités par un même changement ; un écart peut nécessiter plusieurs actions techniques et organisationnelles.

Il faut également examiner les éléments supprimés. Une fonction absente de la cible peut correspondre à une suppression volontaire ou à un oubli. Cette distinction est explicitement soulignée dans les techniques d’analyse des écarts. [3]

## 11. Construire des états de transition cohérents

Une cible peut être pertinente sans être atteignable en une seule étape. Une architecture de transition représente un état intermédiaire cohérent du système, pas simplement une date sur un planning.

Les techniques de planification TOGAF relient les écarts, les solutions et les dépendances aux lots de changement et aux architectures de transition. [3]

Pour notre cas, une trajectoire fictive peut être :

| État | Changement principal | Condition de cohérence |
|---|---|---|
| Initial | Outils et états hétérogènes | Comprendre les autorités réelles |
| Transition 1 | Glossaire des états et références mieux capturées | Compatibilité avec les échanges existants |
| Transition 2 | Consultation unifiée et rapprochement outillé | Autorité d’écriture et reprises définies |
| Cible | Ancien suivi retiré et résolution intégrée | Usages couverts et responsabilités transférées |

Pendant la coexistence, les questions de données deviennent essentielles. Qui peut écrire ? Comment une référence ancienne est-elle retrouvée ? Quelle représentation fait autorité ? Le retour arrière est-il possible après certains changements ?

Un état intermédiaire qui crée deux propriétaires concurrents du résultat n’est pas rendu acceptable par sa courte durée prévue.

## 12. Ordonnancer selon les dépendances et la valeur

Une feuille de route ne doit pas seulement regrouper les travaux par technologie. Elle doit expliquer pourquoi un changement précède un autre et quelle valeur devient disponible après chaque étape.

Dans notre exemple, automatiser le rapprochement suppose des références fiables. Unifier l’affichage suppose un sens défini pour les états. Retirer un outil suppose que ses usages, y compris exceptionnels, soient couverts ailleurs.

```text
Clarifier les états ──────→ Unifier leur présentation
Conserver les références ─→ Fiabiliser le rapprochement
Vérifier les usages ──────→ Retirer l’ancien outil
```

Les dépendances ne sont pas toutes techniques. Une convention avec le partenaire, une habilitation ou une formation peut conditionner la livraison d’une capacité utile.

Le financement doit tenir compte des phases de coexistence. Pendant un temps, l’ancien et le nouveau peuvent devoir être exploités ensemble. Présenter uniquement le coût de la cible masque cet effort et peut rendre la trajectoire irréaliste.

## 13. Réutiliser et mutualiser sans imposer une fausse identité

L’architecture d’entreprise permet de repérer les besoins partagés. Deux domaines peuvent avoir besoin d’authentification, de supervision ou de capacités communes d’intégration. La mutualisation peut réduire certaines duplications, mais elle introduit aussi des dépendances.

Il faut distinguer un service technique commun et un modèle métier universel. Partager un mécanisme d’observation ne signifie pas imposer la même classe de paiement à tous les contextes. De même, deux capacités portant un nom proche peuvent avoir des règles différentes.

Dans notre cas, une consultation commune doit être examinée selon les données et responsabilités qu’elle expose. Si elle assemble plusieurs autorités, elle doit préserver leurs significations plutôt que fabriquer un état unique trompeur.

La question n’est donc pas « peut-on tout centraliser ? », mais « quelle responsabilité commune est stable, utile et exploitable pour ses consommateurs ? ».

## 14. Retirer l’ancien pour réaliser les bénéfices

La transformation ne s’achève pas lorsque le nouveau composant fonctionne. Tant que l’ancien reste nécessaire, certains coûts, risques et dépendances persistent.

Le retrait doit examiner les utilisateurs résiduels, les interfaces, les traitements périodiques, les données à conserver et les besoins de recherche historique. Les obligations de conservation doivent être établies avec les fonctions compétentes ; aucune durée universelle n’est supposée dans notre exemple.

Il faut distinguer arrêter un traitement, supprimer une application et éliminer une donnée. Ces actions n’ont pas les mêmes conséquences. Une ancienne application peut cesser d’écrire tout en restant temporairement nécessaire pour consulter un historique, si cette solution est explicitement retenue et sécurisée.

Les bénéfices doivent être mesurés à leur niveau. Un outil retiré peut réduire des coûts. Un rapprochement plus efficace peut réduire le temps d’assistance. La migration d’un runtime ne prouve pas, seule, l’amélioration du suivi métier.

## 15. Exercice : examiner une cible trop technique

Une proposition de transformation contient une nouvelle plateforme de conteneurs, un broker et plusieurs bases. Elle affirme que ces composants supprimeront les difficultés de suivi et permettront d’arrêter l’ancien système.

Identifier ce qui manque pour établir le lien avec le besoin. Proposer une carte de capacités réduite, trois écarts et un état de transition.

Une réponse argumentée cherche d’abord quelles capacités doivent progresser : suivi, clarification des résultats et information des utilisateurs. Elle demande ensuite comment les états sont définis, quelles références sont conservées et qui traite les exceptions.

Les écarts peuvent porter sur la sémantique, les données et l’organisation plutôt que sur l’hébergement. Un premier état de transition peut améliorer la capture des références avant toute extraction de services. L’arrêt de l’ancien doit attendre la couverture effective de ses usages et la vérification de l’accès aux données nécessaires.

Cette correction n’interdit aucune technologie. Elle montre que la liste des technologies ne constitue pas, à elle seule, une cible d’architecture d’entreprise.

## 16. Une cible n’a de valeur qu’avec un chemin crédible

L’architecture d’entreprise relie les objectifs aux capacités, puis aux changements nécessaires dans les processus, les informations, les applications et les technologies. Elle rend visibles les dépendances que le projet local ne peut pas traiter seul.

L’existant doit être suffisamment connu pour que les écarts soient crédibles. La cible doit décrire un fonctionnement et ses limites. Les transitions doivent préserver un service cohérent, y compris pendant la coexistence. Les bénéfices doivent enfin être reliés à des résultats observables, et non à la seule livraison de composants.

Le chapitre suivant approfondira TOGAF et son Architecture Development Method pour examiner comment organiser ce travail de transformation sans le réduire à une succession mécanique de documents.

## Références

[1] The Open Group, *Business Architecture at Raytheon: A Conversation with J. Bryan Lail*, articulation entre vision, capacités, écarts et solutions : https://blog.opengroup.org/2018/05/01/business-architecture-at-raytheon-a-conversation-with-j-bryan-lail/

[2] The Open Group, *ArchiMate 3.1 Specification: The New Version of the Standard*, distinction entre chaînes de valeur, processus et capacités : https://blog.opengroup.org/2019/11/05/archimate-3-1-specification-the-new-version-of-the-standard/

[3] The Open Group, documentation historique TOGAF, *ADM Support Techniques*, analyse des écarts, dépendances et architectures de transition ; cette référence illustre les techniques, non une exigence de conformité à l’édition actuelle : https://www.opengroup.org/architecture/doc-review/protected-d3/htmlclean/chap27.html

[4] The Open Group, *TOGAF Series Guides*, guides consacrés notamment aux capacités, à leur planification et aux chaînes de valeur : https://www.opengroup.org/togaf/series-guides

[Chapitre précédent](chapitre-04.md) · [Sommaire](../SOMMAIRE.md)
