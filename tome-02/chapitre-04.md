# Chapitre 4 — Concevoir un monolithe modulaire

## 1. Un déploiement commun n’impose pas un modèle unique

Les chapitres précédents ont établi des contextes, des contrats et des agrégats. Rien n’oblige à les distribuer immédiatement entre plusieurs processus. Une application peut être livrée comme une unité tout en conservant des responsabilités internes explicites.

Nous appellerons monolithe modulaire une application déployée comme une unité principale, organisée en modules dont les interfaces, les données et les dépendances sont maîtrisées. Cette définition de travail décrit un objectif de conception ; un ensemble de dossiers ne suffit pas à le réaliser.

Martin Fowler expose l’intérêt de commencer avec une granularité plus large lorsque les frontières restent incertaines, tout en rappelant que le passage ultérieur aux microservices n’est ni automatique ni toujours simple. Son article présente une réflexion issue de retours d’expérience, pas une preuve statistique universelle. [1]

Le monolithe modulaire peut être une cible durable. Sa valeur ne dépend pas de la promesse de le découper un jour en services.

## 2. Séparer les dimensions de modularité

Le mot module peut désigner un regroupement fonctionnel, une unité de compilation, un artefact de construction ou un mécanisme d’encapsulation. Ces dimensions peuvent se compléter sans coïncider.

Un module métier rassemble des responsabilités cohérentes. Un module Maven ou Gradle organise la construction et les dépendances. Un package Java organise les noms et une partie des accès. Aucun de ces moyens ne garantit seul une frontière métier correcte.

| Dimension | Question de conception |
|---|---|
| Fonctionnelle | Quelle responsabilité le module assume-t-il ? |
| Modèle | Quel langage et quelles règles lui appartiennent ? |
| Code | Quels types sont exposés ou internes ? |
| Données | Quelles informations peut-il modifier ? |
| Construction | Quelles dépendances sont disponibles à la compilation ? |
| Exécution | Quelles ressources et transactions sont partagées ? |

Dans notre application fictive, Paiements, Notifications et Résolution peuvent être des modules. Ils peuvent également contenir des subdivisions internes. Le découpage sera jugé à partir des responsabilités et des usages, pas du nombre de projets de construction.

## 3. Organiser par responsabilités plutôt que par dossiers techniques globaux

Une application peut être structurée autour de dossiers globaux `controllers`, `services` et `repositories`. Cette organisation rend les technologies visibles, mais peut disperser une fonctionnalité métier dans tout le code.

Nous proposons de placer les responsabilités métier au premier niveau, puis d’organiser leur intérieur selon les besoins :

```text
com.exemple.banque
├── paiements
│   ├── api
│   └── internal
│       ├── application
│       ├── domaine
│       └── infrastructure
├── notifications
│   ├── api
│   └── internal
└── resolution
    ├── api
    └── internal
```

Il s’agit d’une convention pédagogique. Un module simple n’a pas besoin de trois sous-couches artificielles. Le prochain chapitre examinera les couches en détail ; ici, le point important est la frontière entre modules.

Spring Modulith décrit précisément un module applicatif par une interface fournie, une réalisation interne et les interfaces requises auprès des autres modules. Cette structure illustre les responsabilités à rendre explicites, même sans adopter cet outil. [2]

## 4. Exposer un contrat, pas les objets internes

Le module doit publier les capacités dont les autres ont besoin. Il ne doit pas exposer automatiquement ses entités, ses repositories ou ses mappings de persistance.

Pour une consultation, un contrat peut retourner une représentation immuable, adaptée au besoin et dépourvue de méthode de mutation métier.

```java
package com.exemple.banque.paiements.api;

import java.util.UUID;

public interface ConsultationPaiements {
    VuePaiement consulter(UUID paiementId);
}
```

```java
package com.exemple.banque.paiements.api;

import java.time.Instant;
import java.util.UUID;

public record VuePaiement(
        UUID paiementId,
        String resultatConnu,
        Instant miseAJour) {
}
```

Ces fragments sont illustratifs, non compilés ni exécutés ici. Chaque type public serait placé dans son fichier. Le contrat complet doit encore préciser l’accès, les absences, les erreurs et la signification de la date.

Le choix d’une chaîne pour le résultat garde l’exemple court ; une réalisation devrait définir un vocabulaire public stable et ses règles d’évolution. Retourner l’énumération interne sans examen couplerait les consommateurs au modèle du module.

## 5. Préserver les données de chaque module

Plusieurs modules peuvent utiliser la même instance de base de données sans posséder les mêmes droits logiques sur toutes les tables. L’autorité de modification doit rester explicite.

Dans notre cas, Notifications ne modifie pas le résultat de Paiements. Résolution ne doit pas effectuer un UPDATE direct pour contourner une transition refusée. Les appels passent par les contrats prévus.

| Besoin | Proposition à examiner |
|---|---|
| Lire une information métier | API de consultation ou vue publiée |
| Demander une transition | Cas d’usage exposé par le propriétaire |
| Produire un rapport transverse | Projection ou contrat de lecture explicitement gouverné |
| Corriger une donnée incohérente | Intervention autorisée avec règles et preuves |

Les jointures intermodules peuvent recréer un couplage aux structures internes. Elles ne doivent pas apparaître implicitement dans chaque écran. Une exception de lecture peut être choisie, mais ses dépendances et ses conséquences de migration doivent être connues.

La séparation logique n’est pas un contrôle de sécurité absolu. Si toutes les parties utilisent le même compte SQL, des mesures complémentaires seront nécessaires pour empêcher ou détecter certains contournements.

## 6. Construire un graphe de dépendances lisible

Un module consommateur doit dépendre des contrats publiés, non de la réalisation du fournisseur. Le graphe de code doit éviter les cycles lorsque c’est la convention retenue.

```text
Résolution ──→ API Paiements
Notifications ──→ contrat d’événement publié par Paiements
Paiements ──→ ports internes pour ses dépendances techniques
```

Les flèches indiquent ici des dépendances de code ou de contrat, pas l’ordre de toutes les opérations à l’exécution.

Un cycle peut révéler un partage de responsabilité mal défini. Il peut aussi signaler qu’un contrat est placé dans le mauvais module. Le déplacer vers un dossier `common` n’est pas toujours une correction : cela peut seulement masquer le couplage.

Les tests de cycles sur les packages permettent de contrôler une propriété structurelle. Ils ne détectent pas à eux seuls les boucles de réactions à l’exécution, notamment lorsque des événements déclenchent d’autres événements.

## 7. Choisir les appels directs lorsqu’ils conviennent

Un appel local explicite peut être la solution la plus claire lorsque le demandeur a besoin d’une réponse immédiate et que le contrat est stable.

Dans notre application, Résolution peut demander le dernier état reconnu à Paiements via une interface de consultation. Il n’est pas nécessaire d’introduire un broker pour chaque collaboration.

Il faut néanmoins définir les erreurs, le comportement transactionnel et les droits. Le fait que l’appel ne traverse pas le réseau ne signifie pas qu’il n’a aucune conséquence sur le service appelant.

Une interface locale est aussi un contrat d’évolution. Modifier sa signification peut casser plusieurs modules même si l’ensemble compile. La livraison commune réduit certaines incompatibilités de version, pas les erreurs de sens.

Les appels directs et les événements sont donc des moyens complémentaires. Leur choix doit partir du comportement recherché.

## 8. Utiliser les événements sans confondre découplage et fiabilité

Un événement peut permettre au module producteur de publier un fait sans connaître tous les consommateurs. Spring Modulith recommande ce mode d’interaction pour limiter certaines dépendances entre modules. [3]

La publication Spring est synchrone par défaut. Elle ne devient ni asynchrone ni durable parce que son contenu est appelé événement. La documentation explique aussi que les consommateurs peuvent alors rester associés aux mêmes conditions transactionnelles que l’appel direct, selon la configuration. [3]

| Mode retenu | Conséquence à examiner |
|---|---|
| Traitement synchrone participant à la transaction | Erreurs et durée du consommateur peuvent affecter l’opération initiale |
| Traitement après validation | L’échec du consommateur n’annule plus simplement l’écriture initiale |
| Traitement asynchrone | Concurrence, reprise et état durable deviennent des questions explicites |

Pour notre cas, l’échec d’une livraison de notification ne doit pas forcément annuler la confirmation d’un paiement. Cette propriété doit être réalisée par une coordination adaptée, pas simplement par un nom d’événement.

## 9. Rendre les traitements différés reprenables

Si le producteur a validé son état puis que le processus s’arrête avant le traitement du consommateur, il faut savoir ce qui permet de retrouver le travail restant.

Spring Modulith propose un registre de publication qui enregistre les publications destinées aux listeners transactionnels dans la transaction d’origine, puis marque leur traitement comme terminé lorsqu’il réussit. Des mécanismes permettent de reprendre les publications incomplètes. [3]

Ce registre ne démontre pas, à lui seul, l’unicité de tous les effets externes. Un consommateur qui appelle un service distant doit encore traiter une réponse perdue ou une exécution répétée.

Il faut aussi préciser sa politique de transaction, de nouvelle tentative, de purge et d’observation. La documentation comporte des comportements dépendants des versions ; les paramètres exacts devront être vérifiés pour la version réellement retenue.

La publication durable et l’idempotence seront approfondies dans le tome III. Leur besoin existe déjà dans un seul processus dès que des actions sont différées.

## 10. Définir les transactions sans laisser le module les choisir par accident

Une application unique peut utiliser des transactions locales couvrant plusieurs opérations, si les ressources et les règles de transaction le permettent. Cela ne signifie pas que tout appel interne appartient nécessairement à la même transaction.

Une opération synchrone peut participer à la transaction de l’appelant, ouvrir une autre transaction ou fonctionner sans transaction selon sa réalisation. Cette différence doit être délibérée.

Pour notre exercice, nous proposons que la confirmation du paiement et la conservation de ce qui doit être publié soient coordonnées durablement. La livraison de notification possède ensuite sa propre reprise.

Une transaction traversant plusieurs modules n’est pas interdite par principe. Elle peut néanmoins créer un engagement commun difficile à extraire plus tard. Si ce choix est nécessaire pour une règle immédiate, il faut le reconnaître plutôt que promettre une indépendance inexistante.

L’unité de déploiement et la frontière transactionnelle restent deux décisions distinctes.

## 11. Contrôler les frontières dans le code

La discipline d’équipe est nécessaire, mais elle peut être soutenue par des vérifications automatisées. ArchUnit analyse le bytecode Java et permet notamment de contrôler les dépendances, les couches et les cycles. [4]

L’exemple suivant illustre deux règles pour notre arborescence. Il suppose la dépendance ArchUnit disponible et des classes effectivement compilées sous ce package. Il n’a pas été exécuté ici.

```java
import com.tngtech.archunit.core.domain.JavaClasses;
import com.tngtech.archunit.core.importer.ClassFileImporter;
import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.noClasses;
import static com.tngtech.archunit.library.dependencies.SlicesRuleDefinition.slices;

final class ReglesArchitecture {
    static void verifier() {
        JavaClasses classes = new ClassFileImporter()
            .importPackages("com.exemple.banque");

        noClasses()
            .that().resideOutsideOfPackage(
                "com.exemple.banque.paiements..")
            .should().dependOnClassesThat().resideInAPackage(
                "com.exemple.banque.paiements.internal..")
            .check(classes);

        slices().matching("com.exemple.banque.(*)..")
            .should().beFreeOfCycles()
            .check(classes);
    }
}
```

Une chaîne de test doit appeler cette vérification. L’import doit couvrir les classes pertinentes et ne pas se limiter à un sous-ensemble qui cacherait les violations.

Les règles ne contrôlent pas les accès SQL directs, les échanges construits dynamiquement ou tous les comportements réflexifs. Elles constituent une preuve structurelle partielle, à compléter par les tests adaptés.

## 12. Ce qu’apporte Spring Modulith

Spring Modulith fournit un modèle de modules pour les applications Spring Boot et des capacités de vérification, de test, de documentation et d’observation des interactions. [2][5]

Il ne faut pas confondre la convention de notre exemple avec sa configuration par défaut. Le package de base d’un module est normalement considéré comme son API ; exposer un sous-package `api` demande une déclaration adaptée, par exemple au moyen d’une named interface. [2]

Les détails de configuration doivent être choisis explicitement. Ouvrir largement un module pour faire disparaître les erreurs de vérification peut réintroduire l’accès à ses éléments internes.

L’outil peut aider à maintenir les frontières. Il ne décide pas si Notifications et Paiements correspondent à des responsabilités métier cohérentes. Cette décision reste le résultat de l’analyse du domaine.

Un monolithe modulaire peut également être réalisé avec d’autres technologies et conventions. Spring Modulith est un moyen concret pour notre écosystème Java, pas la définition du style.

## 13. Tester le module puis les collaborations

Les tests doivent distinguer règles internes et collaborations. Un test du domaine protège les invariants ; un test de module vérifie ses cas d’usage ; un test d’intégration transverse examine les contrats et leurs effets.

Spring Modulith permet de démarrer un module seul ou avec une sélection de dépendances dans des tests d’intégration. La documentation explique également qu’un besoin de nombreux composants d’autres modules peut révéler un couplage à réexaminer. [5]

Pour notre exercice :

| Essai | Propriété observée |
|---|---|
| Paiement confirmé selon la règle | Cohérence du domaine |
| Consultation via l’API publiée | Contrat du module |
| Notification en échec | Indépendance de l’effet métier déjà validé |
| Redémarrage avec publications incomplètes | Reprise du travail différé |
| Dépendance interdite ajoutée | Efficacité de la règle structurelle |

Les exemples de code du chapitre ne constituent pas les résultats de ces essais. Ils indiquent ce qu’il faut construire et vérifier.

## 14. Organiser la construction sans créer une illusion d’indépendance

Une application peut être construite avec plusieurs modules Maven ou Gradle puis assemblée en un artefact de déploiement. Cette organisation peut limiter les dépendances disponibles et accélérer certains travaux, selon le projet.

Elle ne garantit pas que seuls les contrats prévus soient consommés. Une dépendance sur l’artefact complet d’un module peut encore donner accès à ses classes publiques internes.

Inversement, une application construite comme un projet unique peut conserver des frontières contrôlées par des règles et des conventions d’accès. La décision doit tenir compte de la taille, des équipes et du coût de maintenance de la construction.

Dans notre cas, nous ne créons pas un sous-projet pour chaque entité. Les unités de construction doivent servir une responsabilité et une contrainte de dépendance identifiées.

## 15. Comprendre les limites opérationnelles

La séparation logique n’apporte pas l’isolation d’un processus distinct. Les modules peuvent partager la mémoire, les threads, les connexions et le cycle de redémarrage.

Si Notifications consomme sans limite une ressource commune, la consultation peut être affectée. Les appels locaux évitent certains coûts réseau, mais pas les problèmes de contention ou de surcharge.

Le dimensionnement porte souvent sur l’unité applicative entière. Si un traitement possède un profil très différent, on peut limiter ses ressources ou étudier un autre déploiement, avec les conséquences associées.

La livraison commune peut être acceptable pour une équipe ou devenir une contrainte pour plusieurs équipes ayant des besoins d’autonomie établis. Elle ne doit être présentée ni comme un défaut universel ni comme un avantage absolu.

Le choix est pertinent lorsqu’il respecte les exigences de service et la capacité d’exploitation de l’organisation.

## 16. Préparer une extraction sans construire des services imaginaires

Des frontières claires facilitent l’examen d’une extraction, mais ne garantissent pas son faible coût. Fowler insiste sur la difficulté de découper un monolithe dont les dépendances sont devenues trop fortes. [1]

Pour extraire Notifications, il faut examiner les contrats, les données, les transactions et les reprises. Un appel local peut devenir indisponible ou lent. Un modèle d’événements local peut devoir devenir un contrat d’intégration versionné.

Il serait donc trompeur de dire qu’il suffit de remplacer une interface par HTTP. La distribution modifie le comportement des défaillances et les conditions de cohérence.

La préparation raisonnable consiste à rendre les dépendances explicites et à supprimer les contournements. Elle ne consiste pas à ajouter un réseau et un broker fictifs à chaque interaction pour anticiper une extraction hypothétique.

Si aucun besoin ne justifie l’extraction, conserver le déploiement commun peut rester la meilleure décision pour le contexte.

## 17. Appliquer le raisonnement à un autre domaine

Dans notre exemple fictif de sinistre, les responsabilités Déclaration, Instruction et Communication peuvent d’abord être réalisées dans une application unique.

La communication ne doit pas décider seule de l’acceptation d’un dossier. L’instruction ne doit pas modifier les données internes d’une déclaration par un accès caché. Les contrats déterminent ce qui est demandé, connu et autorisé.

Les frontières précises restent des hypothèses à examiner avec les règles du cas. Le fait de partager un écran ou un stockage ne démontre pas qu’un seul modèle doit porter toutes les décisions.

Le monolithe modulaire offre ici un moyen d’apprendre et d’évoluer sans introduire immédiatement plusieurs processus. Sa réussite dépend du contrôle réel des responsabilités, pas de l’étiquette employée.

## 18. Exercice : les dossiers séparés qui ne séparent rien

Une application possède les packages Paiements et Notifications. Notifications importe l’entité Paiement, accède à son repository et met à jour sa table. Pour supprimer un cycle, l’équipe déplace les deux repositories dans `common`. Toutes les interactions sont ensuite remplacées par des événements en mémoire.

Identifier pourquoi cette transformation ne démontre pas la modularité. Proposer des corrections et les vérifications associées.

Une réponse argumentée commence par retirer les accès non autorisés au modèle et aux données. Elle définit des contrats de lecture ou d’action et le sens des faits publiés. Elle examine les transactions et la reprise avant de choisir des événements différés.

Le déplacement dans `common` peut masquer le couplage au lieu de le supprimer. Les tests structurels doivent vérifier les dépendances, tandis que les essais d’intégration examinent les effets et les défaillances.

La correction ne consiste pas à introduire obligatoirement un broker. Elle consiste à rendre les responsabilités effectives.

## 19. Une architecture simple exige des frontières réelles

Le monolithe modulaire associe une livraison commune à une conception interne explicite. Les modules possèdent des responsabilités, des contrats et des règles de données ; leurs collaborations et leurs transactions sont délibérées.

Les outils peuvent détecter des violations et soutenir les essais. Ils ne remplacent pas les décisions sur le sens des informations ou l’autorité des changements.

Le chapitre suivant examinera les architectures en couches et N-tier. Nous distinguerons leur séparation logique, leur distribution physique et leur articulation avec les modules métier construits ici.

## Références

[1] Martin Fowler, *Monolith First*, 2015, stratégie de granularité initiale et limites de l’extraction ; réflexion qualitative, non classement universel : https://martinfowler.com/bliki/MonolithFirst.html

[2] Spring, *Spring Modulith — Fundamentals*, interfaces fournies et requises, éléments internes et named interfaces : https://docs.spring.io/spring-modulith/reference/fundamentals.html

[3] Spring, *Working with Application Events*, exécution synchrone, listeners transactionnels et registre de publication : https://docs.spring.io/spring-modulith/reference/events.html

[4] ArchUnit, *User Guide*, import du bytecode, dépendances et cycles ; exemples du chapitre à qualifier dans le projet et la version retenus : https://www.archunit.org/userguide/html/000_Index.html

[5] Spring, *Integration Testing Application Modules*, tests isolés ou avec dépendances et observation des résultats : https://docs.spring.io/spring-modulith/reference/testing.html

[Chapitre précédent](chapitre-03.md) · [Sommaire](../SOMMAIRE.md)
