# Chapitre 7 — SOA et microservices : choisir les frontières utiles

## 1. Le service n’est pas une taille de programme

Une architecture orientée services organise des capacités accessibles par des contrats. Les microservices recherchent notamment une responsabilité cohérente et une autonomie de réalisation et de déploiement. Présenter les premiers comme de gros services et les seconds comme de petits services ne suffit pas à comprendre leurs différences.

SOA peut traiter l’intégration à l’échelle du SI. Les microservices décrivent un style de structuration applicative distribuée. Ces perspectives peuvent se rencontrer ; elles ne constituent pas une échelle automatique du moins moderne au plus moderne. [1][2]

Le choix du protocole ne définit pas à lui seul le style. Une API HTTP mal délimitée peut rester fortement couplée aux autres applications.

## 2. Établir l’autonomie recherchée

Dans notre cas fictif, Notifications pourrait avoir une charge, une équipe et une cadence de livraison différentes de Paiements. Ce sont des motifs à examiner pour un déploiement distinct. Le simple fait que la classe soit dans un package séparé n’en est pas un.

| Besoin | Question à vérifier |
|---|---|
| Livrer séparément | Le contrat permet-il une compatibilité réelle ? |
| Dimensionner séparément | Le profil de charge justifie-t-il la distribution ? |
| Isoler une panne | Les dépendances empêchent-elles sa propagation ? |
| Confier une responsabilité | L’équipe maîtrise-t-elle aussi les données et l’exploitation ? |

La documentation Microsoft associe les microservices à des API définies et à la maîtrise des données par service, tout en décrivant les coûts de cohérence, de réseau et d’exploitation. [2]

## 3. Conserver une forte cohésion

Une frontière utile regroupe ce qui doit évoluer de manière cohérente. Si deux services doivent être modifiés, testés et livrés ensemble pour presque chaque changement, leur autonomie est faible.

Un découpage Paiement, Statut et Montant pourrait produire une chaîne d’appels pour réaliser une seule décision. Il faut alors reconsidérer le modèle, pas simplement optimiser les connexions.

À l’inverse, la notification d’un fait établi peut être distincte de la décision qui l’a produit, si le métier accepte sa livraison différée et si la reprise est maîtrisée.

Une frontière de contexte DDD aide à raisonner. Elle ne détermine pas mécaniquement le nombre de processus, de conteneurs ou de bases physiques.

## 4. Maîtriser les données et les contrats

Le propriétaire d’un service doit conserver une autorité explicite sur ses écritures. Les autres consomment ses contrats plutôt que de modifier ses tables internes.

Cela n’impose pas nécessairement un serveur de base distinct par service. Cela impose d’examiner l’accès, le schéma et les conditions d’évolution. Une base commune accessible sans restriction peut recréer un monolithe distribué.

Une projection locale peut réduire les appels, mais elle introduit une fraîcheur et une reconstruction à concevoir. Le refus de toute duplication peut, lui aussi, augmenter les dépendances.

Les contrats doivent définir les faits, les erreurs, les versions et les limites. Déployer séparément tout en partageant une bibliothèque de modèle qui exige des mises à jour simultanées peut annuler une partie du bénéfice.

## 5. Traiter les communications comme des défaillances possibles

Un appel local devenu distant peut être lent, refusé ou interrompu. Les appels en chaîne cumulent les attentes et peuvent propager une indisponibilité. Microsoft recommande notamment d’éviter les API trop bavardes et les dépendances longues. [2]

```text
Parcours fragile : canal → suivi → paiement → résolution → partenaire
Option à étudier : suivi lit des faits publiés ; résolution travaille séparément
```

La seconde option n’est valable que si ses données et son retard répondent au besoin. L’asynchronisme ne garantit pas la correction ni la reprise.

Les timeouts, reprises et protections de ressources doivent avoir un budget de bout en bout. Un retry ne doit pas être ajouté sans examiner la répétition de l’effet métier.

## 6. La plateforme ne remplace pas le contrat métier

Une plateforme peut gérer placement, redémarrage et certaines communications. Elle ne sait pas, à elle seule, interpréter une confirmation ou résoudre une contradiction de résultats.

La gateway peut fournir un point d’entrée et des fonctions transverses. Elle ne devrait pas devenir le lieu caché de toutes les règles métier. Chaque opération sensible doit rester correctement autorisée dans les chemins réellement accessibles.

Les équipes doivent pouvoir suivre une demande entre services, identifier les versions et reprendre le travail interrompu. Sans cette capacité, le découpage peut dépasser la maturité opérationnelle disponible.

## 7. Décider une extraction limitée

Pour extraire Notifications, nous proposons d’identifier les événements consommés, de supprimer ses accès aux tables de Paiements et de définir la reprise avant la séparation physique.

Il faut ensuite vérifier la compatibilité, les doublons, le retard de livraison et l’indisponibilité du consommateur. L’extraction n’est pertinente que si les bénéfices dépassent les coûts introduits.

Exercice : une équipe veut transformer tous les modules en microservices pour réduire le couplage. Une réponse argumentée distingue couplage logique et distribution, examine les données et propose une extraction motivée plutôt qu’un découpage uniforme.

Conserver un monolithe modulaire peut rester une décision aboutie. La qualité vient de l’adéquation aux exigences, pas du nombre de services.

## Références

[1] The Open Group, *Service-Oriented Architecture*, présentation de SOA et articulation avec TOGAF : https://pubs.opengroup.org/onlinepubs/7699929599/toc.pdf

[2] Microsoft Learn, *Microservices architecture style*, propriétés, coûts et antipatterns : https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/microservices

[Précédent](chapitre-06.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-08.md)
