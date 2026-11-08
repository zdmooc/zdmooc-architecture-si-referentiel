# Chapitre 13 — BIAN et les responsabilités bancaires

## 1. Utiliser un référentiel de responsabilités

Le Banking Industry Architecture Network publie notamment le Service Landscape, une structure de référence qui organise ses Service Domains. BIAN précise que différentes classifications peuvent présenter le même ensemble de domaines. Il ne s’agit donc pas simplement du schéma de déploiement d’une application. [1]

Le référentiel peut aider une banque à discuter des responsabilités et des échanges avec un vocabulaire partagé. Il ne fournit pas, seul, les règles particulières, les habilitations et les engagements de chaque établissement.

## 2. Ne pas confondre les niveaux de conception

| Élément | Question traitée dans notre démarche |
|---|---|
| Capacité métier | Ce que l’organisation doit savoir faire |
| Service Domain BIAN | Responsabilité du référentiel étudié |
| Contexte DDD | Limite de validité d’un modèle local |
| Service déployé | Unité d’exécution et de livraison |
| Contrat d’échange | Engagement concret entre participants |

Nous proposons d’établir des correspondances argumentées entre ces éléments. Leur nom proche n’établit pas une équivalence automatique. Un Service Domain ne doit pas être transformé mécaniquement en microservice ni en agrégat transactionnel.

Le découpage réel dépend aussi du patrimoine, des équipes, des exigences de cohérence et de la trajectoire.

## 3. Examiner les API sémantiques

Le dépôt officiel publie les BIAN Semantic APIs et leurs artefacts par release. Il présente notamment des formes OpenAPI et AsyncAPI ainsi que des variantes de modèles. [2]

Une description d’interface facilite l’étude des opérations et des informations. Elle ne prouve pas que deux réalisations partagent les mêmes garanties d’idempotence, de délai, d’ordre ou de résultat.

L’intégration doit préciser ces aspects, les erreurs, les droits, les références et la compatibilité. Adopter un nom d’opération ne fournit pas à lui seul le processus de reprise.

## 4. Appliquer sans rebaptiser artificiellement le cas

Dans notre étude, nous distinguons réception d’intention, interaction d’exécution, connaissance du résultat, investigation et information client. Ce sont des responsabilités proposées pour le cas fictif, pas une nomenclature officielle BIAN.

Le travail consiste à examiner les descriptions de la release choisie, relever les rapprochements et les écarts, puis décider des frontières utiles. Une couche d’adaptation peut protéger le modèle local du vocabulaire externe, sans prétendre supprimer les différences de sens.

Les décisions conservent la source, la version et la justification. Une similarité de noms ne constitue pas une validation métier.

## 5. Prévoir l’évolution du référentiel

BIAN indique que certaines releases renomment, retirent ou ajoutent des Service Domains, notamment dans les paiements. [1] La version étudiée doit donc être figée pour une comparaison reproductible.

L’équipe examine ensuite les changements sémantiques et les conséquences sur contrats, mapping, documentation et tests. Les nombres de domaines ou d’API ne constituent pas une preuve de couverture de son SI.

L’alignement avec d’autres standards doit aussi être étudié sur les messages et règles effectivement employés, sans promettre une conformité à partir d’une simple correspondance de champs.

## 6. Exercice : un microservice par domaine

Un programme impose un déploiement par Service Domain sans analyser données ni parcours.

La correction conserve l’utilité du référentiel mais distingue cartographie et réalisation. Elle propose une correspondance vérifiée sur un parcours limité, des frontières de cohérence explicites et une trajectoire compatible avec l’exploitation.

## Références

[1] BIAN, *Service Landscape* : https://bian.org/deliverables/service-landscape/

[2] BIAN, dépôt officiel *public — Semantic APIs* : https://github.com/bian-official/public

[Précédent](chapitre-12.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-14.md)
