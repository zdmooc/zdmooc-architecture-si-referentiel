# Chapitre 2 — Les niveaux et les vues d’architecture

## 1. Pourquoi un seul schéma ne suffit pas

Lors d’une réunion, une carte du système de paiements est projetée. Elle contient des acteurs, des applications, des files de messages, des bases de données et des serveurs. Le métier demande où se trouve la règle d’autorisation. L’exploitation demande quelle panne interrompt les consultations. La sécurité demande quelles données traversent la frontière du partenaire. Le développement demande quel module doit être modifié pour ajouter une transition.

Le dessin paraît complet, mais il ne répond clairement à aucune de ces questions. Les objets ne sont pas décrits au même niveau et les relations n’ont pas toutes le même sens. Une flèche représente parfois une dépendance, parfois une transmission, parfois un simple regroupement.

Le problème n’est pas l’absence de détails. C’est l’absence de perspective explicite. Une représentation utile sélectionne les informations nécessaires à une question. Elle laisse les autres à des représentations complémentaires.

Il faut ainsi distinguer l’architecture du système et sa description. La norme ISO/IEC/IEEE 42010 porte sur les descriptions d’architecture et les concepts permettant de les organiser ; elle ne fournit pas, à elle seule, l’architecture à adopter. [1]

## 2. Vue, point de vue, modèle et diagramme

Une partie prenante porte des préoccupations concernant le système : fonctionnalité, coût, disponibilité, sécurité, maintenabilité ou exploitation. Un point de vue définit les conventions utilisées pour examiner certaines de ces préoccupations. Une vue applique ces conventions au système considéré. [2]

L’analogie avec un plan est utile. Les conventions d’un plan d’évacuation indiquent quels éléments montrer et comment les interpréter. Le plan d’évacuation d’un bâtiment particulier en est une application. De la même manière, un point de vue de déploiement peut définir les types de nœuds et de relations à représenter ; la vue de déploiement du système de paiements décrit les instances et ressources choisies pour ce système.

Un modèle organise des éléments et leurs relations pour une analyse. Un diagramme en donne une représentation graphique. La vue ne se réduit pas nécessairement à un dessin : elle peut s’appuyer sur plusieurs représentations et sur des explications textuelles. La distinction sert à éviter de prendre un outil de dessin pour une méthode de raisonnement. [2]

| Notion | Question | Exemple dans notre cas |
|---|---|---|
| Préoccupation | Que faut-il comprendre ? | La consultation survit-elle à la panne d’un processus ? |
| Point de vue | Avec quelles conventions l’examiner ? | Montrer instances, nœuds et dépendances d’exécution |
| Vue | Quelle est la réponse pour ce système ? | Répartition des instances et dépendances partagées |
| Diagramme | Comment représenter cette réponse ? | Dessin des nœuds et des communications |

Ces définitions sont ici reformulées pour l’apprentissage. Un travail de conformité à une norme nécessite d’en examiner les exigences exactes, pas seulement d’en reprendre le vocabulaire.

## 3. Le niveau d’architecture n’est pas la vue

Un niveau détermine l’étendue et la profondeur de l’analyse. Une vue détermine les préoccupations que l’on examine. Les deux dimensions se croisent.

À l’échelle de l’entreprise, une vue peut montrer les capacités métier ou les applications qui les soutiennent. À l’échelle d’une solution, elle peut montrer les responsabilités des composants ou leur comportement en cas de panne. À l’échelle d’une application, elle peut montrer les modules du code ou les processus à l’exécution.

Dans cet ouvrage, nous utiliserons la distinction pratique suivante, sans en faire une nomenclature universelle :

| Niveau | Périmètre principal | Question typique |
|---|---|---|
| Entreprise | Ensemble du SI et trajectoire globale | Quelles capacités et applications faut-il faire évoluer ? |
| Domaine | Périmètre métier ou technique cohérent | Quels modèles, règles et principes partager ? |
| Solution | Réponse de bout en bout à un besoin | Quels composants doivent collaborer ? |
| Application | Structure d’un logiciel | Comment organiser les responsabilités internes ? |
| Plateforme | Services d’hébergement et d’exploitation | Quelles capacités communes fournir ? |

Ces niveaux ne correspondent pas automatiquement à cinq équipes ni à cinq documents. Un architecte peut en couvrir plusieurs. Inversement, une solution importante peut mobiliser plusieurs spécialistes au même niveau.

## 4. La vue de contexte : tracer la frontière

La première difficulté consiste souvent à décider ce qui appartient au système étudié. Une vue de contexte place ce système parmi ses utilisateurs et ses dépendances externes. Dans C4, le diagramme de contexte remplit cette fonction au premier niveau de représentation. [3]

Pour notre cas fictif, le système de suivi des paiements reçoit les demandes des clients, permet à l’assistance de les consulter et échange avec un partenaire d’exécution.

```text
[Client] ── soumet et consulte ──────────┐
                                       ▼
                              [Système de paiements]
                                       ▲       │
[Assistance] ── recherche et explique ───┘       │ demande l’exécution
                                               ▼
                                   [Partenaire d’exécution]
```

Le contexte ne prétend pas montrer les packages Java, les bases ou les serveurs. Il répond à d’autres questions : qui utilise le système, quels services lui sont demandés et de quels acteurs externes dépend-il ?

Le partenaire reste hors de la frontière parce que nous ne maîtrisons pas son fonctionnement interne. Il n’est pas nécessaire de dessiner une base supposée chez lui. Représenter une structure non connue donnerait une fausse impression de maîtrise.

Une frontière doit être nommée. « Système de paiements » peut désigner une application ou un ensemble de plusieurs applications ; la vue doit expliquer le périmètre retenu.

## 5. La vue métier : comprendre l’activité

Le métier ne se limite pas aux écrans. Il comprend des responsabilités, des règles et des enchaînements qui peuvent exister indépendamment du logiciel actuel.

Dans notre exemple, le parcours comporte la réception d’une demande, l’examen de son admissibilité, sa soumission à l’exécution et le suivi du résultat. Cette description doit préciser les variantes : rejet, correction, attente ou résultat encore inconnu.

```text
Recevoir la demande
        ↓
Évaluer son admissibilité
        ├── non admissible → Expliquer le rejet
        └── admissible
                ↓
        Demander l’exécution
                ↓
        Établir le résultat connu
                ↓
        Informer et permettre le suivi
```

Cet enchaînement est une proposition pour le cas fictif. Il ne constitue pas un processus bancaire standard.

Il faut également distinguer capacité et processus. Une capacité décrit ce que l’organisation sait accomplir ; un processus décrit un enchaînement d’activités dans une situation. Dans notre vocabulaire de travail, « assurer le suivi des paiements » est une capacité, tandis que les étapes de traitement d’une demande décrivent un processus. Cette convention sera approfondie avec l’architecture métier.

## 6. La vue applicative : attribuer les responsabilités

Une vue applicative montre les éléments logiciels pertinents et leurs relations. Il faut préciser si les éléments représentent des applications indépendantes, des services ou des modules internes. Utiliser le même rectangle pour tous sans légende rend la lecture ambiguë.

Dans notre première solution, les responsabilités restent regroupées dans une application modulaire :

```text
Application de paiements
┌────────────────────────────────────────────┐
│ Réception des demandes                     │
│             ↓                              │
│ Gestion du cycle de vie                     │
│       ├──→ Adaptation du partenaire          │
│       └──→ Consultation des états            │
└────────────────────────────────────────────┘
```

Les flèches représentent ici des interactions possibles entre responsabilités, pas un ordre temporel complet ni des dépendances réseau. Le diagramme ne suffit donc pas à savoir quand une donnée est sauvegardée ou comment une erreur est traitée.

La vue devient utile lorsqu’elle permet d’attribuer une règle. Qui décide qu’un paiement peut être soumis ? Qui traduit les réponses du partenaire ? Qui expose un état consultable ? Une responsabilité partagée sans règle explicite peut annoncer une incohérence future.

## 7. La vue des données : sens, autorité et circulation

Dessiner une base ne décrit pas l’architecture des données. Il faut comprendre les informations, leur signification, leur autorité et leur circulation.

Pour le suivi, distinguer le paiement, la tentative d’interaction et la vue présentée à l’utilisateur. Un paiement peut donner lieu à plusieurs échanges techniques. Une projection de consultation peut exposer un résultat dérivé sans devenir l’autorité qui décide de la transition métier.

| Information | Autorité proposée dans le cas fictif | Question à résoudre |
|---|---|---|
| État métier du paiement | Responsabilité Paiements | Quelles preuves autorisent la transition ? |
| Référence du partenaire | Adaptation de l’exécution | Comment retrouver une demande ? |
| Dernier état affiché | Consultation | Quel retard est acceptable ? |
| Historique des interactions | Intégration | Que conserver pour le rapprochement ? |

Le modèle conceptuel décrit les notions. Le modèle logique organise les données pour une solution. Le modèle physique précise leur réalisation dans un stockage. Ces distinctions pratiques évitent de partir du nom d’une table pour conclure à une frontière métier.

Une vue de circulation doit aussi montrer les copies et les transformations. Si la consultation possède une copie d’un état, il faut expliquer comment elle est alimentée et ce qu’il se passe lorsque son actualisation prend du retard.

## 8. La vue dynamique : suivre une situation dans le temps

La structure répond à « qui peut collaborer ? ». La vue dynamique répond à « comment ces éléments collaborent-ils dans ce scénario ? ». C4 propose un diagramme dynamique en complément de ses vues de structure. [3][4]

Dans notre cas, une séquence nominale peut être représentée ainsi :

```text
Client       Application       Stockage       Partenaire
  │              │                │               │
  ├─ demande ───►│                │               │
  │              ├─ enregistrer ─►│               │
  │              │◄─ confirmation─┤               │
  │              ├─ soumettre avec référence ────►│
  │              │◄──────── résultat connu ───────┤
  │              ├─ enregistrer ─►│               │
  │◄─ résultat ──┤                │               │
```

Cette illustration ne définit pas à elle seule les frontières transactionnelles ni le protocole réel du partenaire. Elle les rend discutables. Que représente exactement le premier enregistrement ? La confirmation garantit-elle la durabilité attendue ? Que se passe-t-il si le résultat est reçu mais que le dernier enregistrement échoue ?

Il faut ensuite dessiner le scénario où le partenaire traite la demande sans que l’application reçoive sa réponse. La séquence doit faire apparaître le résultat indéterminé, la conservation de la référence et la recherche ultérieure du statut.

Limiter la vue dynamique au succès empêche souvent de voir les décisions les plus importantes.

## 9. La vue de développement : organiser les dépendances du code

Une organisation fonctionnelle ne montre pas nécessairement comment le logiciel est construit. La vue de développement s’intéresse aux modules, packages, bibliothèques et dépendances.

Pour une conception à ports et adaptateurs, nous pourrions retenir :

```text
Adaptateur HTTP ───────→ Application ───────→ Domaine
                             │
                             ▼
                       Ports sortants
                             ▲
                             │
           Adaptateurs SQL et partenaire
```

Les flèches désignent ici des dépendances du code. L’adaptateur technique dépend du contrat qu’il réalise. À l’exécution, le cas d’usage appellera néanmoins un objet qui réalise ce contrat. Les deux représentations sont compatibles parce qu’elles ne montrent pas la même relation.

Cette distinction résout une confusion fréquente : une flèche d’exécution vers une base n’implique pas que le domaine importe directement les classes du moteur de stockage.

La vue de développement doit aussi faire apparaître les dépendances interdites. Si le domaine peut appeler directement le contrôleur HTTP, l’organisation réelle contredit la séparation annoncée, même si les dossiers portent les bons noms.

## 10. La vue de déploiement : placer les instances

Une vue de déploiement représente l’installation des instances logicielles sur des ressources d’exécution pour un environnement donné. C4 distingue explicitement ses diagrammes de déploiement des représentations de structure. [3][4]

```text
Entrée réseau
      │
      ├──────────→ Instance A de l’application
      │                         │
      └──────────→ Instance B de l’application
                                │
                A et B ─────────┼──→ Stockage partagé
                                └──→ Partenaire externe
```

Deux instances ne suffisent pas à prouver la disponibilité de bout en bout. Il reste à examiner l’entrée réseau, le stockage, les dépendances externes et les modes communs de défaillance. Le dessin indique une répartition ; l’analyse établit ce qu’elle permet réellement de supporter.

Il faut nommer l’environnement. Une vue de production ne doit pas laisser croire que les mêmes redondances existent en développement. Il faut aussi distinguer unité logicielle et instance : la même application peut être instanciée plusieurs fois sans devenir plusieurs responsabilités métier.

## 11. La sécurité et l’exploitation traversent les vues

Une vue de sécurité ne se résume pas à ajouter un cadenas entre deux rectangles. Les préoccupations de sécurité modifient les questions posées aux autres représentations.

Dans le contexte, qui appartient à un domaine de confiance différent ? Dans les données, quelles informations sont sensibles ? Dans la dynamique, où l’identité et l’autorisation sont-elles contrôlées ? Dans le déploiement, où les flux sont-ils autorisés et comment les secrets sont-ils fournis ?

L’exploitation traverse également plusieurs vues. La structure indique les dépendances à surveiller ; les séquences indiquent les étapes où mesurer le temps ; les états métier indiquent les situations anormales ; le déploiement indique les composants dont la perte doit être détectée.

Ces éléments sont des questions de conception pour notre cas, pas un référentiel complet de sécurité ou d’exploitation. Ils montrent pourquoi une propriété transverse ne peut pas être déduite d’un seul schéma.

## 12. Le modèle 4+1 : relier structure et scénarios

Philippe Kruchten propose une description autour de quatre vues : logique, processus, développement et physique. Les scénarios constituent le « +1 » : ils servent à découvrir, illustrer et valider la collaboration des éléments représentés dans les autres vues. [5]

| Vue du modèle 4+1 | Attention principale |
|---|---|
| Logique | Abstractions et structure fonctionnelle |
| Processus | Concurrence, synchronisation et comportements d’exécution |
| Développement | Organisation statique du logiciel dans son environnement de construction |
| Physique | Répartition du logiciel sur les ressources matérielles |
| Scénarios | Interactions significatives reliant les autres vues |

La vue de processus ne doit pas être confondue avec un processus métier : elle traite notamment des aspects de concurrence et de synchronisation de l’exécution. [5]

Dans le cas des paiements, un scénario de réponse perdue permet de vérifier plusieurs vues à la fois : le modèle distingue-t-il l’incertitude ? Quel processus reprend le travail ? Quel module le réalise ? Sur quel environnement s’exécute-t-il ?

Le modèle n’exige pas de répéter le même diagramme quatre fois avec des noms différents. Son intérêt réside dans les préoccupations distinctes et dans les liens entre elles.

## 13. C4 : choisir le bon niveau de zoom

C4 organise la représentation du logiciel autour de quatre niveaux : contexte du système, conteneurs, composants et code. Il propose également des diagrammes complémentaires de paysage, de dynamique et de déploiement. Tous les niveaux ne sont pas nécessaires pour chaque situation. [3][4]

Le contexte montre le système et son environnement. Le niveau conteneurs ouvre sa frontière pour montrer les principales applications et unités de stockage. Le niveau composants examine l’intérieur d’un conteneur. Le niveau code apporte une profondeur supplémentaire lorsque celle-ci est utile.

Le mot « conteneur » est ici un piège de vocabulaire. Dans C4, il ne signifie pas obligatoirement conteneur Docker. Une application et un stockage peuvent être représentés comme des conteneurs C4 sans préciser à ce stade leur emballage technique. C4 est fondé sur des abstractions logicielles, pas sur l’obligation d’utiliser une plateforme particulière. [3]

Pour notre système fictif, le contexte montre clients et partenaire. Le niveau conteneurs peut distinguer interface web, application de traitement et stockage. Le niveau composants peut ensuite ouvrir l’application de traitement pour montrer cas d’usage, gestion des paiements et adaptation du partenaire.

C4 ne remplace pas toutes les analyses. Un diagramme de conteneurs ne définit pas les règles de cohérence d’un agrégat et ne démontre pas un mécanisme de reprise. Il faut conserver les modèles et scénarios qui répondent à ces préoccupations.

## 14. Ne pas fusionner les classifications

TOGAF distingue des domaines d’architecture, C4 organise des niveaux de représentation du logiciel et 4+1 répartit des préoccupations dans plusieurs vues. Les faire correspondre terme à terme produirait des équivalences trompeuses.

La vue logique de 4+1 n’est pas simplement « le niveau composants de C4 ». Un contexte C4 n’est pas, à lui seul, toute l’architecture métier. Les capacités d’une plateforme ne sont pas décrites uniquement par une vue physique.

La bonne articulation consiste à choisir les conventions qui répondent au besoin de lecture, puis à maintenir les correspondances entre les représentations. Il vaut mieux deux vues ciblées et cohérentes qu’un assemblage exhaustif de classifications sans question directrice.

## 15. Maintenir les correspondances entre les vues

La cohérence doit pouvoir se vérifier en suivant un élément.

Prenons la responsabilité « retrouver un résultat inconnu ». Elle apparaît dans le processus métier comme une activité de clarification. Elle est portée par une responsabilité applicative de rapprochement. Elle utilise la référence conservée dans les données. Elle apparaît dans une séquence de reprise. Elle est réalisée par du code et exécutée par une instance identifiée.

Si le schéma de déploiement ne contient aucun processus pouvant réaliser ce rapprochement, il manque une correspondance. Si la donnée nécessaire n’est pas conservée, le processus métier promis n’est pas réalisable. Si l’application l’exécute mais qu’aucun acteur ne traite ses échecs, la responsabilité opérationnelle reste incomplète.

| Élément à suivre | Correspondances à retrouver |
|---|---|
| Soumission du paiement | Cas d’usage, contrat partenaire, référence persistée, séquence |
| Consultation du statut | Modèle de lecture, autorisation, fraîcheur et exposition |
| Reprise après incident | État conservé, déclencheur, traitement et responsable |

Le but n’est pas d’imposer un tableau administratif à chaque lecteur. Il est de tester que les différentes représentations parlent bien du même système.

## 16. Reconnaître les dessins trompeurs

Un rectangle intitulé « service » sans précision peut représenter une capacité métier, une classe, une application ou une ressource réseau. Un cylindre partagé peut masquer plusieurs autorités de données. Une flèche « API » ne dit pas si le traitement est synchrone, quelle opération est appelée ou ce qu’un succès signifie.

Une autre erreur consiste à mélanger modèle cible et existant sans distinction. Le lecteur croit alors qu’une dépendance est déjà supprimée ou qu’un mécanisme de reprise existe, alors qu’il s’agit d’une intention.

Pour rendre un dessin interprétable, son titre doit annoncer son objet et sa perspective. Les éléments doivent être typés, les relations nommées et les frontières expliquées. Une information inconnue doit rester explicitement inconnue plutôt qu’être inventée pour compléter le dessin.

Enfin, une vue peut révéler une question sans y répondre. Dessiner deux instances et une base partagée conduit à examiner cette base ; cela ne valide pas automatiquement sa résilience.

## 17. Exercice : décomposer une vue surchargée

Un schéma contient un client, une règle d’admissibilité, un contrôleur HTTP, un cluster, une table de paiements et un partenaire externe. Toutes les relations portent le mot « utilise ».

Produire trois représentations : une vue de contexte, une vue des responsabilités internes et une séquence de réponse perdue. Expliquer ce qui doit être retiré de chacune, puis identifier les questions qui nécessitent une vue de données ou de déploiement.

La vue de contexte conserve le client, le système étudié et le partenaire. Le contrôleur et la table ne sont pas nécessaires à son objectif. La vue interne distingue réception, décision métier et adaptation externe sans ajouter immédiatement les nœuds de déploiement. La séquence montre l’envoi, l’absence de résultat connu et le mécanisme de recherche proposé.

La conservation durable de la référence exige ensuite un examen des données et de la persistance. La survie du mécanisme de recherche à la perte d’une instance exige une analyse d’exécution et de déploiement. Le passage d’une vue à l’autre révèle ainsi des questions qui restaient cachées dans le dessin initial.

## 18. Représenter moins, expliquer mieux

Une description d’architecture gagne en valeur lorsqu’elle facilite une décision ou permet de vérifier une propriété. Sa qualité ne se mesure pas au nombre de rectangles ni au nombre de notations employées.

Le contexte délimite le système. Les vues métier et applicative distribuent les responsabilités. Les données précisent le sens et l’autorité de l’information. La dynamique expose les collaborations et les échecs. Le développement contrôle les dépendances du code. Le déploiement relie les unités logicielles à leurs conditions d’exécution.

Aucune de ces perspectives ne dispense des autres lorsqu’elles sont nécessaires. Leur articulation permet de passer d’une intention compréhensible à un comportement réalisable. Le chapitre suivant examinera les acteurs qui construisent, arbitrent et exploitent ces représentations.

## Références

[1] ISO, *ISO/IEC/IEEE 42010:2022 — Software, systems and enterprise — Architecture description*, présentation de la portée : https://www.iso.org/standard/74393.html

[2] ISO/IEC/IEEE 42010, site de présentation, *Conceptual Model*, parties prenantes, préoccupations, points de vue et vues : http://www.iso-architecture.org/ieee-1471/cm/

[3] Simon Brown, *The C4 model for visualising software architecture*, abstractions et représentations : https://c4model.com/

[4] C4 model, *Diagrams*, niveaux de zoom et diagrammes complémentaires : https://c4model.com/diagrams

[5] Philippe Kruchten, *The 4+1 View Model of Architecture*, IEEE Software, 1995, exemplaire hébergé par le MIT : https://web.mit.edu/16.35/www/lecturenotes/Kruchten4+1.pdf

[Chapitre précédent](chapitre-01.md) · [Sommaire](../SOMMAIRE.md)
