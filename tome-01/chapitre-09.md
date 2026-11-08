# Chapitre 9 — SABSA et l’architecture de sécurité

## 1. Protéger une activité, pas seulement installer des contrôles

Notre système fictif de paiements dispose d’une authentification, d’un réseau filtré et de communications chiffrées. Peut-on en conclure que sa sécurité est correctement conçue ? Pas encore. Ces mécanismes ne disent pas si un agent peut consulter le paiement d’un autre périmètre, si une reprise peut produire un second effet ou si une confirmation provient réellement de l’autorité attendue.

La première question n’est donc pas « quels produits de sécurité ajouter ? », mais « quelles propriétés du service faut-il préserver et contre quelles situations ? ». Cette question relie la sécurité au métier et aux conséquences concrètes d’une défaillance.

Le SABSA Institute présente SABSA comme une méthode d’architecture de sécurité pilotée par le métier, les risques et les opportunités, applicable aux niveaux entreprise et solution. La traçabilité vers les objectifs métier fait partie de ses caractéristiques centrales. [1]

La sécurité devient alors une contribution au fonctionnement attendu, et non un ensemble de dispositifs ajoutés à la fin de la conception.

## 2. La portée de SABSA

SABSA comprend des cadres et techniques liés aux exigences métier, au risque, aux politiques, aux services de sécurité, aux domaines, à la gouvernance et à la gestion des services sur leur cycle de vie. [1]

Son ambition dépasse le dessin d’un réseau sécurisé. Les personnes, les procédures et les technologies participent ensemble à la protection. Le SABSA Institute souligne notamment que la capacité à soutenir un service ne peut pas être obtenue par la technologie seule. [2]

Dans notre cas, sécuriser la résolution d’un résultat inconnu nécessite un mécanisme de contrôle des accès, mais aussi une définition des personnes habilitées, une procédure de décision et un moyen de vérifier le résultat.

Ce chapitre explique les principes et propose un cas pédagogique. Il ne reproduit pas l’ensemble de la méthode et ne constitue ni une démonstration de conformité réglementaire ni une architecture de sécurité prête pour la production.

## 3. Partir des objectifs et de la valeur à préserver

La sécurité ne concerne pas seulement la confidentialité des informations. Dans le suivi des paiements, une information fausse, une opération non autorisée ou une impossibilité durable de résoudre les exceptions peuvent également compromettre la valeur du service.

Le métier doit préciser les conséquences : exposition d’une donnée, état présenté à tort comme définitif, relance injustifiée, impossibilité d’expliquer une intervention. Ces situations ne doivent pas être regroupées dans une catégorie vague de risque technique.

| Objectif du cas fictif | Propriété recherchée |
|---|---|
| Permettre une consultation légitime | Limiter l’accès au périmètre autorisé |
| Présenter un résultat compréhensible | Préserver le sens et l’origine de la confirmation |
| Résoudre les exceptions | Encadrer les interventions et les reprises |
| Maintenir le service utile | Prévoir des modes dégradés maîtrisés |
| Expliquer les actions sensibles | Conserver les éléments de preuve nécessaires |

Ce tableau est une proposition de conception. Il ne reproduit pas le catalogue officiel des attributs SABSA.

L’identification de ces propriétés permet ensuite d’examiner les risques et de choisir des contrôles proportionnés.

## 4. Le Business Attribute Profile

Le Business Attribute Profile, ou BAP, exprime les besoins métier sous forme d’attributs contextualisés. Les explications du SABSA Institute associent chaque attribut à une méthode de mesure, à un indicateur et à une cible. Elles relient ces objectifs à l’appétence au risque. [3][4]

La démarche rejoint le travail sur les attributs de qualité du chapitre 4, mais l’inscrit explicitement dans la justification de la sécurité et du risque. Un terme comme « traçable » doit recevoir un sens opérationnel dans le contexte étudié.

Pour notre service, nous pouvons proposer :

| Propriété formulée pour l’exercice | Observation envisagée | Critère à définir |
|---|---|---|
| Consultation correctement autorisée | Tests d’accès hors périmètre et observations en exploitation | Couverture, détection et traitement attendus |
| Intervention explicable | Reconstitution d’un échantillon d’actions sensibles | Informations requises et délai de recherche |
| Reprise maîtrisée | Exercices de résultat inconnu et de répétition | Effets acceptables et conditions de reprise |
| Service soutenable en incident | Exercices avec les acteurs habilités | Capacité de diagnostic et de rétablissement |

Les valeurs doivent venir des besoins, des obligations et des capacités réelles. Une cible chiffrée ne prouve pas sa propre pertinence.

Un taux de tests réussis ne mesure pas à lui seul le risque restant. Il donne une preuve dans un périmètre d’essai, avec des hypothèses qui doivent rester connues.

## 5. Examiner risques et opportunités

SABSA considère le risque comme une incertitude sur les résultats, en tenant compte des menaces mais aussi des opportunités de création de valeur. [3]

Dans notre cas, ouvrir un meilleur accès au suivi peut réduire les sollicitations d’assistance. Cette opportunité crée aussi une exposition supplémentaire si les contrôles de périmètre sont insuffisants. Supprimer toute possibilité de consultation éviterait certains accès indésirables, mais détruirait une partie du service recherché.

L’analyse doit préciser le scénario et ses conséquences. Une menace est une source possible de dommage ; une vulnérabilité est une faiblesse exploitable ; le risque concerne la possibilité et l’impact d’un résultat indésirable dans le contexte étudié. Ces définitions servent ici de vocabulaire de travail.

| Situation fictive | Conséquence redoutée | Question prioritaire |
|---|---|---|
| Consultation d’un paiement hors périmètre | Divulgation d’informations | Où la décision d’accès est-elle appliquée ? |
| Confirmation non reconnue comme légitime | État métier incorrect | Comment sa provenance et son sens sont-ils vérifiés ? |
| Relance d’une demande déjà exécutée | Second effet métier | Quel contrat permet une répétition sûre ? |
| Usage abusif d’une fonction de reprise | Action non autorisée | Quels droits et quelles validations sont nécessaires ? |

Ces scénarios sont des supports d’analyse, pas des constats sur une organisation réelle. Ils ne justifient pas une quantification arbitraire de probabilité.

## 6. Descendre du contexte vers la réalisation

La présentation usuelle de SABSA distingue les perspectives contextuelle, conceptuelle, logique, physique et composants, complétées par la gestion opérationnelle des services de sécurité. Cette dernière traverse les autres perspectives plutôt que de représenter seulement une dernière étape. Une synthèse de ces couches est proposée par MethodAtlas. [5]

| Perspective | Question appliquée à notre cas |
|---|---|
| Contextuelle | Quelle valeur du suivi doit être préservée et pourquoi ? |
| Conceptuelle | Que signifie une résolution maîtrisée et explicable ? |
| Logique | Quels services de décision d’accès, de preuve et de contrôle faut-il ? |
| Physique | Quels mécanismes et quelle répartition les réalisent ? |
| Composants | Quels produits, paramètres et configurations sont retenus ? |
| Gestion des services | Comment maintenir, mesurer et faire évoluer la protection ? |

Le tableau est une reformulation pédagogique, pas une reproduction de la matrice SABSA.

Le passage entre niveaux doit préserver le sens. Une exigence de reprise autorisée ne se réalise pas seulement par un chiffrement réseau. Il faut relier la propriété aux décisions, aux identités, aux opérations permises et aux traces nécessaires.

## 7. Couvrir les six interrogations

SABSA examine les perspectives selon six interrogations : quoi, pourquoi, comment, qui, où et quand. [5]

Dans notre cas, « quoi » conduit à identifier les données, les fonctions et la valeur concernées. « Pourquoi » renvoie aux objectifs et aux risques. « Comment » conduit aux services et mécanismes. « Qui » précise les acteurs et mandats. « Où » examine les frontières et la distribution. « Quand » fait apparaître les événements, les périodes de validité et les conditions temporelles.

Cette dernière dimension est souvent sous-estimée. Un droit accordé lors d’une prise de poste peut ne plus être légitime après un changement de mission. Une autorisation de reprise exceptionnelle ne doit pas devenir un privilège permanent sans réexamen.

La structure aide à poser les questions. Elle ne démontre pas qu’une réponse est exacte ni qu’un contrôle est efficace. Compléter une grille n’élimine pas les hypothèses fragiles.

## 8. La traçabilité dans les deux sens

Le SABSA Institute décrit une traçabilité bidirectionnelle. Un risque métier doit pouvoir être relié aux contrôles qui le traitent ; les mesures de ces contrôles doivent permettre de revenir vers l’évaluation du risque réellement supporté. [4]

```text
Objectif métier
      ↓
Propriété à préserver
      ↓
Scénario de risque
      ↓
Service de sécurité
      ↓
Mécanisme réalisé
      ↓
Preuves et mesures
      └────────→ réévaluation du risque et des décisions
```

Dans notre exemple, le risque de consultation illégitime conduit à une décision d’autorisation appliquée au paiement concerné. Les preuves incluent des essais hors périmètre et l’observation des refus. Un simple inventaire du fournisseur d’identité ne démontre pas cette propriété.

Dans l’autre sens, un contrôle existant doit avoir une justification identifiable : besoin métier, obligation ou traitement de risque. Il ne doit pas être supprimé au seul motif que sa justification est absente du dossier actuel ; cette absence appelle d’abord une recherche.

## 9. Politiques, services, mécanismes et produits

Une politique exprime une règle ou une orientation de protection. Un service de sécurité fournit une capacité logique qui contribue à la réaliser. Un mécanisme décrit comment cette capacité fonctionne. Un produit et sa configuration en fournissent une réalisation concrète.

Pour notre cas, une politique pourrait limiter la consultation aux acteurs autorisés sur le périmètre concerné. Le service logique prend une décision d’accès. La réalisation applique cette décision au point où l’opération sur le paiement est effectivement demandée.

L’organisation peut utiliser des composants différents pour authentifier l’acteur, porter son contexte et contrôler son droit sur la ressource. Le choix exact doit être étudié ; aucune technologie particulière n’est imposée par notre exemple.

Cette décomposition évite deux erreurs. La première est de croire qu’un produit installé réalise toutes les politiques. La seconde est d’écrire des règles sans moyen de les appliquer et de les vérifier.

## 10. Identité et autorisation ne sont pas synonymes

L’authentification vise à établir une identité selon le niveau de confiance attendu. L’autorisation décide si une action est permise dans un contexte. Un utilisateur authentifié peut ne pas avoir le droit de consulter une opération particulière.

Dans notre conception pédagogique, l’accès doit tenir compte de l’action, de la ressource et du périmètre attribué. Un rôle large comme « assistance » ne suffit pas nécessairement à déterminer les droits sur tous les paiements.

Les identités techniques doivent être examinées séparément. Une application authentifiée auprès d’une autre application n’a pas automatiquement le droit de modifier tout état métier. Il faut également comprendre si elle agit pour elle-même ou dans le cadre d’une demande utilisateur.

Le NIST distingue explicitement authentification et autorisation dans sa présentation de l’architecture Zero Trust. [6] Cette distinction permet de ne pas confondre possession d’un justificatif et permission d’exécuter une opération.

## 11. Domaines de confiance et Zero Trust

Une frontière de confiance marque un changement d’hypothèses ou d’autorité. Elle peut se trouver entre l’utilisateur et l’application, entre domaines internes, ou entre l’organisation et le partenaire.

La présence d’un composant sur le réseau interne ne constitue pas à elle seule une preuve de confiance. Le NIST précise que Zero Trust n’accorde pas de confiance implicite sur le seul fondement de la localisation physique ou réseau, ni de la propriété de l’équipement. La protection porte sur les ressources et leurs accès. [6]

SABSA et Zero Trust ne sont pas deux noms pour la même chose. Dans notre articulation, SABSA aide à justifier les propriétés et services de protection ; les principes Zero Trust peuvent contribuer à certaines décisions de contrôle des accès. Ils ne répondent pas à toute la gestion des risques et des opportunités.

Pour notre système, le partenaire doit être reconnu selon un contrat technique et métier. Un canal chiffré peut contribuer à protéger l’échange sans prouver que chaque code de résultat a le sens attendu par le domaine Paiements.

## 12. Encadrer les reprises sensibles

La reprise est un bon révélateur de l’architecture de sécurité. Une action destinée à corriger une situation peut produire un dommage si elle est accordée trop largement ou exécutée avec une compréhension incomplète.

Dans notre cas fictif, nous proposons que l’action soit liée à une situation identifiée, à un droit explicite et à une référence stable. Certaines opérations peuvent nécessiter une approbation supplémentaire ou une séparation des responsabilités, selon le risque établi. Il ne s’agit pas d’une règle universelle applicable à toute action.

La séparation doit être réelle. Deux validations n’apportent pas la garantie recherchée si elles reposent sur la même identité ou si un accès direct permet de contourner le parcours.

Il faut aussi traiter les interventions d’urgence. Leur périmètre, leur durée, leurs conditions d’activation et leur revue ultérieure doivent être explicites. Une urgence ne transforme pas automatiquement tous les accès en actions légitimes.

## 13. Protéger les données et les preuves

La protection doit suivre la donnée dans ses usages : échange, stockage, copie de consultation, journal, sauvegarde et export. Les propriétés requises ne sont pas nécessairement identiques partout.

Le chiffrement contribue à la confidentialité dans les conditions de son emploi. Il ne remplace pas le contrôle d’accès, la maîtrise des clés ou la réduction des données exposées. Une application autorisée à déchiffrer peut encore présenter l’information à une personne non habilitée.

Pour notre cas, les traces doivent permettre d’expliquer une action sans recopier inutilement les données sensibles. Il faut préciser les acteurs, la référence, l’action, son résultat et les éléments de contexte réellement nécessaires.

Conserver une trace ne prouve pas automatiquement son intégrité ni sa valeur probante. Les conditions de production, de protection, d’horodatage et d’accès doivent être étudiées. Les obligations de conservation demandent une analyse du contexte ; aucune durée légale universelle n’est supposée ici.

## 14. Sécurité et continuité doivent être arbitrées ensemble

Une décision de protection peut affecter la disponibilité. Si un service d’autorisation devient inaccessible, faut-il refuser l’action, utiliser une décision encore valide ou proposer un mode dégradé ? La réponse dépend de l’opération et du risque.

Dans notre cas, une reprise financière sensible et une consultation non sensible ne doivent pas être traitées automatiquement de la même manière. Le comportement en défaillance doit être défini avant l’incident, avec les acteurs habilités.

L’objectif n’est pas de choisir systématiquement la sécurité contre la disponibilité. Il est de préciser ce qui reste permis, avec quelles limites et pour quelle durée. Le SABSA Institute souligne la nécessité d’équilibrer les attributs plutôt que de les traiter en silos. [2]

Une solution qui ne peut être maintenue, diagnostiquée ou rétablie par les équipes disponibles peut compromettre la valeur qu’elle prétend protéger.

## 15. Vérifier ce qui est réalisé

La présence d’un contrôle ne suffit pas : il faut observer son fonctionnement dans les situations pertinentes. Les essais doivent couvrir les autorisations attendues et les refus, les chemins directs, les erreurs et les opérations exceptionnelles.

| Propriété proposée | Vérification dans notre cas |
|---|---|
| Accès limité au périmètre | Demander un paiement d’un autre périmètre |
| Reprise réservée aux acteurs habilités | Appeler l’opération sans droit, y compris hors écran |
| Confirmation issue de l’autorité attendue | Vérifier l’origine et le traitement des entrées non reconnues |
| Action sensible explicable | Reconstituer son déroulement depuis les éléments conservés |
| Protection maintenue en panne | Simuler l’indisponibilité d’une dépendance de sécurité |

Ces essais doivent être autorisés et conduits dans un environnement approprié. Leur résultat reste limité au périmètre testé ; aucun ensemble fini de tests ne démontre l’absence de toute vulnérabilité.

La mesure en exploitation complète cette qualification. Une absence d’alerte peut signifier absence de situation détectée ou absence de visibilité. Il faut vérifier que les signaux existent et que leur traitement possède un responsable.

## 16. Risque résiduel et décision

Après les contrôles, certaines incertitudes subsistent. Le risque résiduel doit être évalué dans son contexte, avec les limites des preuves obtenues.

Le SABSA Institute relie le niveau acceptable à l’appétence au risque et insiste sur le retour des mesures vers la compréhension du risque conservé. [3][4]

Dans notre organisation fictive, l’architecte explique les options, leurs coûts et leurs limites. L’acceptation doit relever de l’autorité mandatée, non être déduite d’un silence ou d’une signature technique sans contexte.

Une exception doit également pouvoir être réexaminée. Une hypothèse partenaire peut changer, un contrôle compensatoire peut devenir insuffisant et une solution temporaire peut durer plus longtemps que prévu. La décision doit rester liée aux conditions qui la justifient.

## 17. Articuler SABSA avec TOGAF et DDD

Le SABSA Institute présente l’intégration avec d’autres cadres, dont TOGAF, comme un usage de la méthode. [1] Il ne s’agit pas d’ajouter une sécurité isolée après les phases d’architecture.

Dans notre articulation pédagogique, le cadrage de transformation précise les objectifs et le périmètre. Le travail SABSA aide à définir les propriétés de protection et leur justification. Le DDD précise les concepts, les invariants et les frontières du modèle. La conception technique réalise et vérifie les mécanismes correspondants.

Ces échanges sont itératifs. Une règle d’autorisation peut révéler une frontière métier mal définie. Une exigence de preuve peut imposer une information persistée. Une limitation d’exploitation peut conduire à revoir un mécanisme trop complexe.

Aucune adoption de référentiel ne vaut conformité automatique à toutes les obligations applicables. La méthode aide à organiser le raisonnement et les preuves ; les exigences spécifiques doivent encore être identifiées et évaluées.

## 18. Exercice : une sécurité apparente

Une équipe affirme que le service est sécurisé parce que les utilisateurs sont authentifiés, les échanges chiffrés et les composants placés sur un réseau interne. Une fonction de relance est accessible à tous les agents d’assistance.

Identifier les propriétés non démontrées et proposer une chaîne reliant objectif métier, risque, service de sécurité et preuve.

Une réponse argumentée demande d’abord qui peut consulter et reprendre quelle opération. Elle distingue l’identité du droit, la protection du transport du sens du résultat et la localisation réseau de l’autorisation. Elle examine aussi la répétition d’une demande déjà exécutée.

La chaîne proposée peut partir de l’objectif « résoudre une situation sans action indue », identifier le risque de reprise injustifiée, définir un service d’autorisation contextualisé et demander des essais de refus, de contournement et de répétition. La sûreté de l’effet distant exige en plus le contrat métier et technique du partenaire.

L’exercice montre qu’une liste de produits ne remplace pas l’analyse des situations à protéger.

## 19. Une sécurité justifiée, réalisée et maintenue

SABSA relie les besoins métier, les attributs, les risques et les services de protection. Sa force est de rendre la justification des contrôles explicite et de ramener les observations vers l’évaluation du risque.

La chaîne doit rester entière : une politique sans mécanisme est une intention ; un mécanisme sans exploitation peut perdre son efficacité ; une mesure sans contexte peut donner une confiance injustifiée.

Dans notre cas des paiements, la sécurité concerne autant la légitimité d’une consultation que la maîtrise d’une reprise et la capacité à expliquer une intervention. Le chapitre suivant examinera comment explorer les besoins et coordonner la réalisation avec Design Thinking et l’agilité à l’échelle.

## Références

[1] The SABSA Institute, *SABSA Executive Summary*, portée, cadres intégrés et orientation métier, risques et opportunités : https://sabsa.org/sabsa-executive-summary/

[2] The SABSA Institute, *The Attributer’s Blog — Supportable*, articulation personnes-processus-technologies et équilibre des attributs : https://sabsa.org/the-attributers-blog-supportable/

[3] The SABSA Institute, *The Attributer’s Blog — In Control*, Business Attribute Profile, mesures et appétence au risque : https://sabsa.org/the-attributers-blog-in-control/

[4] The SABSA Institute, *The Attributer’s Blog — Traceable*, traçabilité bidirectionnelle et retour des mesures : https://sabsa.org/the-attributers-blog-traceable/

[5] Accenture, MethodAtlas, *SABSA Security Architecture*, synthèse des perspectives et de la gestion des services ; présentation secondaire, distincte des textes de l’Institut : https://accenture.github.io/MethodAtlas/sabsa/

[6] NIST, *SP 800-207 — Zero Trust Architecture*, 2020, confiance implicite, ressources, authentification et autorisation : https://csrc.nist.gov/pubs/sp/800/207/final

[Chapitre précédent](chapitre-08.md) · [Sommaire](../SOMMAIRE.md)
