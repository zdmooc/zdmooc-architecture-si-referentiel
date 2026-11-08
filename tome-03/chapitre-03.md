# Chapitre 3 — Concevoir une architecture événementielle

## 1. Distribuer les réactions à un fait

Une architecture événementielle organise des producteurs, des canaux et des consommateurs. Elle permet de faire réagir plusieurs responsabilités sans transformer le producteur en orchestrateur de chacune d’elles. Microsoft en décrit les bénéfices mais aussi les difficultés de cohérence, d’ordre et d’observation. [1]

Dans notre étude, Paiements peut annoncer une confirmation ; Consultation actualise une vue et Notifications prépare une information. Ces réactions ne doivent pas réécrire la confirmation simplement parce qu’elles ont échoué.

## 2. Choisir la topologie selon les consommateurs

```text
Paiements → canal de faits
               ├── abonnement Consultation → instances concurrentes
               └── abonnement Notifications → instances concurrentes
```

Deux instances d’un même consommateur peuvent se répartir le travail. Deux responsabilités indépendantes doivent chacune recevoir les faits qui les concernent. Une seule file partagée entre elles peut distribuer les messages au lieu de diffuser chaque fait aux deux.

Un journal rejouable et une diffusion avec conservation limitée ne présentent pas les mêmes possibilités de reconstruction. Il faut vérifier le produit, sa configuration et la disponibilité de l’historique, plutôt que déduire la rejouabilité du mot événement. [1]

## 3. Définir le contenu et l’autorité

Un événement riche peut permettre une réaction sans rappel immédiat au producteur. Un événement ne contenant qu’une référence impose souvent une consultation supplémentaire, avec ses droits, sa disponibilité et sa fraîcheur. Microsoft décrit ce compromis entre données incluses et clés seules. [1]

Le consommateur doit savoir si les données décrivent le fait à son occurrence ou l’état courant retrouvé ensuite. Les deux peuvent différer.

Une copie de consultation ne devient pas l’autorité de modification. Les données sensibles demandent un périmètre de diffusion et une conservation maîtrisés.

## 4. Concevoir l’ordre utile

Chercher un ordre global pour tout le SI peut introduire une coordination inutile. Notre besoin peut se limiter à l’ordre des évolutions d’une demande.

La clé de partitionnement et la concurrence doivent alors préserver les propriétés nécessaires. L’ordre de livraison ne garantit pas l’ordre de fin des traitements si plusieurs tâches travaillent en parallèle.

Une projection de changements successifs doit détecter un trou de séquence. Une projection de snapshots complets peut appliquer une politique de remplacement par version. Ces stratégies ne sont pas interchangeables.

## 5. Observer les parcours et les boucles

La publication réussie ne signifie pas que tous les consommateurs ont avancé. Chaque responsabilité doit rendre visibles son retard, ses erreurs et ses travaux bloqués.

Une réaction qui republie un changement peut créer une boucle. La revue doit suivre les causes et distinguer nouvelle décision, répétition technique et simple projection. Ajouter des événements ne constitue pas, seul, un découplage sémantique.

## 6. Exercice : une notification bloque la consultation

Les deux consommateurs partagent une file et un message invalide est remis immédiatement en tête. Proposer une correction.

La réponse sépare les abonnements, borne les reprises, définit une quarantaine et vérifie si l’ordre métier autorise de poursuivre. Elle établit aussi une responsabilité de reprise : déplacer le message ne résout pas sa cause. Le chapitre suivant examine les cohérences réellement protégées.

## Référence

[1] Microsoft Learn, *Event-driven architecture style* : https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/event-driven

[Précédent](chapitre-02.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-04.md)
