# Chapitre 8 — Concevoir les API et les contrats

## 1. Publier un engagement compréhensible

Une API ne se limite pas à une URL et à un schéma. Son contrat définit les opérations, les conditions d’accès, le sens des données et ce qu’une réponse permet d’affirmer.

Dans notre système fictif, créer une demande de paiement et confirmer son exécution sont des engagements distincts. Le protocole doit rendre cette différence visible.

HTTP possède ses propres sémantiques de méthodes, de représentations et de codes de réponse. Les conventions applicatives doivent les respecter plutôt que les remplacer par une interprétation locale silencieuse. [1]

## 2. Choisir les ressources et les intentions

Une proposition pourrait exposer une ressource de demandes et une ressource de consultation de leur résultat. Les noms ci-dessous sont pédagogiques, non une spécification bancaire.

```text
POST /demandes-paiement
GET  /demandes-paiement/{id}
POST /demandes-paiement/{id}/recherches
```

GET ne doit pas être utilisé pour déclencher une action métier telle qu’une resoumission. Une opération de recherche ayant des effets persistants peut être représentée séparément de la simple lecture d’un résultat.

Le modèle d’échange ne doit pas copier automatiquement l’entité interne. Il expose ce qui est utile et autorisé, avec unités, formats, bornes et vocabulaire définis.

| Élément | Question contractuelle |
|---|---|
| Identité | Identifie-t-on la demande, un message ou une tentative ? |
| Montant | Quelle devise, représentation et précision ? |
| Résultat | Quelle autorité et quel niveau de certitude ? |
| Date | Quel événement représente-t-elle et dans quel format ? |

## 3. Distinguer réception et résultat

Le code 201 indique la création d’une ressource. Il ne signifie pas, par lui-même, qu’un paiement externe a été exécuté. Le code 202 indique une acceptation pour traitement, sans promettre l’achèvement. [1]

Une réponse doit permettre de retrouver le travail reçu et d’en connaître les suites. Si un résultat différé est prévu, le contrat doit expliquer interrogation, notification ou événement, ainsi que les erreurs possibles.

```json
{
  "demandeId": "demande-123",
  "priseEnCharge": "RECUE",
  "resultatConnu": "INDETERMINE"
}
```

Ce format est une proposition originale. Les valeurs doivent être stabilisées avec le métier et les consommateurs. La date de réponse n’est pas nécessairement la date du dernier fait métier.

## 4. Concevoir les erreurs

Une erreur de syntaxe, un accès refusé, une transition impossible et une indisponibilité ne doivent pas être confondus.

| Situation | Interprétation à conserver |
|---|---|
| Demande mal formée | L’entrée ne respecte pas le contrat |
| Action interdite | L’acteur n’a pas le droit requis |
| Conflit d’état | La situation actuelle empêche l’action |
| Précondition de version non satisfaite | La représentation utilisée n’est plus celle attendue |
| Dépendance indisponible | Le traitement ne peut pas être établi normalement |

HTTP distingue notamment 409 pour un conflit et 412 pour une précondition non satisfaite. Leur usage doit être cohérent avec le contrat. [1]

Un corps d’erreur peut exposer un code applicatif stable et une référence de diagnostic. Il ne doit pas révéler requêtes SQL, secrets ou détails sensibles. Le message humain peut évoluer sans devenir le seul moyen de décision automatisée.

## 5. Répétition et concurrence

L’idempotence d’une méthode HTTP ne suffit pas à déduire l’unicité d’un effet métier de bout en bout. POST n’est pas défini comme idempotent ; une application peut néanmoins lui associer un protocole de reconnaissance des intentions répétées. [1]

Dans notre conception, une clé d’intention possède un périmètre, une durée de reconnaissance et une règle pour un contenu différent sous la même clé. Deux demandes concurrentes doivent être arbitrées durablement.

Pour une modification conditionnelle, un validateur tel qu’un ETag et une précondition If-Match peuvent contribuer à éviter l’écrasement d’une représentation devenue ancienne. Il faut encore relier cette précondition au contrôle transactionnel réel. [1]

Un timeout ne définit pas une autorisation de resoumettre. Le contrat doit expliquer comment retrouver une demande dont le résultat est inconnu.

## 6. Faire évoluer les contrats

Ajouter un champ facultatif peut être compatible pour certains consommateurs. Ajouter une valeur d’énumération peut casser un consommateur qui suppose une liste fermée. Renommer un statut peut changer le sens du parcours même si le JSON reste valide.

L’étude doit donc distinguer compatibilité syntaxique et sémantique. Les exemples de contrat doivent inclure succès, attentes, absences et erreurs, pas seulement une réponse nominale.

Les événements publiés demandent la même discipline : nom du fait, identité, données, version et règles de consommation. Ils ne doivent pas devenir une sérialisation des objets de persistance.

## 7. Borner les usages et vérifier le contrat

Pagination, taille des messages, filtres, fréquence et coûts doivent être bornés selon l’usage. Une API de consultation sans limite peut affecter la disponibilité du service.

Les droits doivent être vérifiés sur l’action, l’objet et les propriétés exposées. OWASP distingue explicitement ces risques dans son API Security Top 10. [2]

Exercice : une API retourne 200 avec « succès » après tout envoi au partenaire. La correction doit définir ce qui est réellement acquis, proposer une ressource de suivi et examiner la répétition, les erreurs et les autorisations.

Un contrat réussi permet au consommateur de savoir ce qu’il peut faire ensuite sans reconstruire les détails internes du fournisseur.

## Références

[1] IETF, *RFC 9110 — HTTP Semantics*, sections 9, 13 et 15 : https://www.rfc-editor.org/rfc/rfc9110.html

[2] OWASP Developer Guide, *API Top 10* : https://devguide.owasp.org/en/07-training-education/07-api-top-ten/

[Précédent](chapitre-07.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-09.md)
