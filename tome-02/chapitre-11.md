# Chapitre 11 — Vérifier les règles et les dépendances

## 1. Choisir la preuve selon la promesse

Une architecture peut promettre des invariants, des frontières de code, une transaction cohérente et une reprise après incident. Aucun test unique ne démontre toutes ces propriétés.

Il faut relier chaque affirmation à une preuve appropriée. Le but n’est pas d’obtenir un pourcentage flatteur, mais de rendre les comportements attendus vérifiables et leurs limites connues.

| Promesse | Preuve à construire |
|---|---|
| Transition interdite refusée | Test du domaine |
| API interne inaccessible à un autre module | Vérification structurelle |
| Écriture concurrente arbitrée | Essai avec transactions indépendantes |
| Droit appliqué sur le bon objet | Test d’autorisation via le chemin réel |
| Publication récupérable après arrêt | Essai de reprise avec état durable |

Les exemples du livre indiquent des comportements à vérifier. Ils ne constituent pas un ensemble déjà exécuté ou une qualification de production.

## 2. Tester le domaine sans masquer ses hypothèses

L’agrégat du chapitre 3 peut être créé et soumis avant une confirmation. Les essais doivent couvrir les transitions permises, les références invalides, les répétitions et les contradictions.

Il faut observer aussi l’état après un refus. Une exception attendue ne suffit pas si des propriétés ont déjà été modifiées de manière incohérente.

Des données fixes et des dépendances temporelles contrôlables facilitent les scénarios reproductibles. Une règle dépendant du temps doit distinguer la date métier et l’horloge technique utilisée pour l’évaluer.

Un test unitaire ne valide pas l’identité de l’appelant ni le comportement du partenaire. Il démontre une règle locale sous les entrées qui lui sont fournies.

## 3. Vérifier les frontières structurelles

ArchUnit permet d’analyser les dépendances du code compilé et d’exprimer des règles de couches, de packages et de cycles. [1]

Il faut tester la vérification elle-même : introduire une violation contrôlée dans un exercice permet de vérifier qu’elle est effectivement détectée. Un import incomplet peut produire un résultat rassurant tout en ignorant les classes importantes.

Une règle de package ne détecte pas tous les accès construits dynamiquement, les requêtes SQL ou les boucles d’événements. Elle doit être complétée par des contrats et des essais d’exécution.

Le contrôle des dépendances ne décide pas non plus de la pertinence métier du découpage. Une structure sans cycle peut rester mal alignée avec les responsabilités.

## 4. Tester les adaptateurs et les modules

Un adaptateur en mémoire peut permettre d’isoler le cœur. Il ne doit pas être pris pour un stockage équivalent : conserver des références d’objets peut cacher l’absence de sauvegarde et les règles transactionnelles.

Les adaptateurs réels demandent des essais sur leurs conversions, leurs erreurs et leurs engagements. Une suite commune peut vérifier certains comportements partagés, mais les particularités du moteur restent à examiner.

Spring Modulith propose des tests d’intégration de modules, isolés ou avec leurs dépendances choisies. Cette capacité aide à distinguer le périmètre vérifié et peut révéler un couplage excessif. [2]

Un contrat consommateur-fournisseur doit inclure les variantes d’erreur et les nouvelles valeurs possibles, pas seulement la forme d’une réponse nominale.

## 5. Reproduire la concurrence sans compter sur le hasard

Pour examiner deux confirmations concurrentes, les transactions doivent charger une version comparable, puis tenter les modifications selon un ordonnancement maîtrisé. Des points de synchronisation sont préférables à un simple délai supposé suffisant.

Il faut vérifier qu’un conflit ne se transforme pas en écrasement silencieux, que les faits durables restent cohérents et qu’une nouvelle tentative relit les informations nécessaires.

Un test utilisant le même contexte de persistance pour les deux opérations peut masquer le scénario réel. La version du moteur, l’isolation et les conditions d’accès doivent être celles que l’essai prétend qualifier. PostgreSQL documente précisément les différences entre ses niveaux d’isolation. [3]

## 6. Observer les transactions et les accès réels

Une méthode annotée n’est pas une preuve de transaction. Un essai doit provoquer un échec à une étape pertinente et observer les données réellement conservées. Avec Spring en mode proxy, l’appel doit traverser le mécanisme effectif. [4]

De même, un test de sécurité qui remplace le filtre par un faux contexte ne vérifie pas l’ensemble du parcours d’authentification et d’autorisation. Les essais doivent couvrir les refus et les accès alternatifs. [5]

Les tests rapides et les essais plus coûteux se complètent. Il faut organiser leur exécution pour détecter tôt les défauts locaux sans reporter toutes les propriétés importantes à un environnement final.

## 7. Injecter des incidents et conserver une conclusion honnête

Notre étude doit interrompre le traitement avant l’envoi, après réception distante, après écriture et après publication. Chaque perturbation répond à une question différente.

L’observation porte sur l’état durable, le travail restant, les effets visibles et les moyens de reprise. Une exception technique n’est pas le seul résultat intéressant.

Exercice : un test affirme prouver l’absence de doublon après deux appels successifs. La correction doit distinguer répétition séquentielle, concurrence, redémarrage et effet distant. Elle propose les preuves supplémentaires plutôt que de généraliser le premier résultat.

Une conclusion utile conserve les conditions de l’essai et ce qui reste inconnu. C’est cette précision qui transforme une démonstration en élément de décision.

## Références

[1] ArchUnit, *User Guide* : https://www.archunit.org/userguide/html/000_Index.html

[2] Spring Modulith, *Integration Testing Application Modules* : https://docs.spring.io/spring-modulith/reference/testing.html

[3] PostgreSQL, *Transaction Isolation* : https://www.postgresql.org/docs/current/transaction-iso.html

[4] Spring Framework, *Using @Transactional* : https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html

[5] OWASP, *Authorization Cheat Sheet* : https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html

[Précédent](chapitre-10.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-12.md)
