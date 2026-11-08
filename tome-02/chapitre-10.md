# Chapitre 10 — Intégrer la sécurité dans la conception applicative

## 1. Traduire les objectifs en décisions d’accès

Le tome I a relié sécurité, objectifs et risques. La conception applicative doit maintenant déterminer où ces règles sont appliquées et comment les chemins de contournement sont évités.

Une identité reconnue ne donne pas accès à toutes les ressources. OWASP distingue authentification et autorisation et recommande de limiter les privilèges, de refuser par défaut et de vérifier les permissions à chaque requête. [1]

Dans notre système fictif, consulter, demander une recherche et intervenir sur une situation sensible sont trois actions différentes. Un rôle Assistance ne suffit pas nécessairement à autoriser toutes les opérations.

## 2. Construire un contexte de confiance

L’adaptateur d’entrée établit le contexte à partir de justificatifs validés selon le mécanisme retenu. Le cœur ne doit pas considérer un identifiant d’utilisateur ou un rôle transmis librement dans le contenu comme une preuve de droit.

Les identités techniques demandent également un périmètre. Une application qui s’authentifie n’est pas automatiquement habilitée à agir sur tous les paiements de tous les clients.

Le NIST rappelle que la localisation réseau ne fonde pas, à elle seule, la confiance. [2] Un appel interne doit donc être examiné selon le contrat, l’acteur et la ressource, pas seulement selon son origine technique.

Dans une architecture hexagonale, le cœur peut utiliser une représentation du contexte et un contrat d’autorisation. Les détails de validation des justificatifs restent dans les mécanismes adaptés.

## 3. Protéger action, objet et propriétés

| Niveau | Question pour notre cas |
|---|---|
| Action | L’acteur peut-il ouvrir une investigation ? |
| Objet | Peut-il agir sur cette demande particulière ? |
| Propriétés | Quels champs peut-il consulter ou modifier ? |
| Contexte | Une condition particulière limite-t-elle le droit ? |

Les vérifications doivent couvrir le chemin réel d’exécution. Masquer un bouton ne protège pas l’API. Générer un identifiant difficile à deviner ne remplace pas le contrôle sur l’objet. OWASP insiste sur ces deux points. [1]

Une requête de lecture doit rester limitée au périmètre légitime. Une liste filtrée correctement ne suffit pas si l’accès direct par identifiant ignore la même règle.

L’état métier peut aussi limiter une action. Avoir un droit d’intervention ne signifie pas qu’une transition interdite par le modèle devient valide.

## 4. Contrôler les entrées et les dépendances

La validation doit borner formats, tailles et valeurs selon le contrat. Elle ne remplace pas les règles métier. Les propriétés modifiables doivent être sélectionnées explicitement, plutôt que copiées intégralement depuis le contenu reçu vers une entité.

Les réponses du partenaire ne doivent pas être considérées comme sûres uniquement parce qu’elles arrivent d’un système connu. Le format, la provenance attendue et le sens doivent être interprétés avant d’affecter le modèle.

Les consommations de ressources doivent être limitées : recherches coûteuses, exports, pages volumineuses et répétitions peuvent nuire au service. OWASP identifie notamment les risques d’autorisation sur les propriétés, de consommation non bornée et d’usage abusif des flux sensibles. [3]

Une erreur de validation ou d’accès doit produire un comportement maîtrisé, sans divulgation de détails internes.

## 5. Réduire les privilèges techniques et les données exposées

Les comptes de stockage, les secrets et les accès administratifs doivent correspondre aux besoins de réalisation. Une application qui utilise un compte surpuissant peut contourner involontairement ses propres frontières.

Les traces doivent permettre de comprendre une action sans recopier secrets et données sensibles. Leur accès, leur conservation et leur intégrité doivent être conçus selon le contexte.

Le chiffrement protège certains usages dans les conditions de sa réalisation. Il ne remplace pas la maîtrise des clés ni l’autorisation de l’utilisateur qui reçoit ensuite la donnée.

La politique d’erreur ne doit pas davantage révéler un paiement sensible par un message trop précis. Le contrat doit décider ce qui peut être annoncé selon le droit de l’appelant.

## 6. Traiter les interventions et les pannes

Une fonction de reprise peut nécessiter des droits, une justification et une approbation supplémentaire selon le risque. Ces conditions doivent être appliquées au point d’exécution, pas seulement dans un écran.

Si une dépendance d’autorisation ne répond plus, le comportement doit être défini pour chaque opération. Il ne faut pas transformer automatiquement cette panne en accès accordé.

Les accès d’urgence demandent un périmètre, une durée et une revue adaptés. Une exception opérationnelle ne doit pas devenir un chemin permanent non contrôlé.

## 7. Tester les refus autant que les succès

OWASP recommande des tests unitaires et d’intégration des décisions d’accès, sans les présenter comme un remplacement d’une évaluation de sécurité spécialisée. [1]

Dans notre cas, les essais doivent comprendre une demande d’un autre périmètre, une action sans droit, une propriété non modifiable, un appel hors écran et une dépendance défaillante.

Exercice : la gateway authentifie tous les appels ; le backend autorise toute consultation reçue. La correction distingue authentification et droit sur l’objet, identifie les chemins alternatifs et vérifie où la décision complète peut être appliquée.

Une architecture applicative sécurisée ne se résume pas au placement d’un filtre. Elle relie les permissions, les règles métier et la réalisation de tous les chemins pertinents.

## Références

[1] OWASP, *Authorization Cheat Sheet* : https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html

[2] NIST, *SP 800-207 — Zero Trust Architecture* : https://csrc.nist.gov/pubs/sp/800/207/final

[3] OWASP Developer Guide, *API Top 10* : https://devguide.owasp.org/en/07-training-education/07-api-top-ten/

[Précédent](chapitre-09.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-11.md)
