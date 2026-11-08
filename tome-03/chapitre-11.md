# Chapitre 11 — Résilience : délais, reprises et isolation

## 1. Préserver le service pendant une perturbation

La résilience vise une réponse acceptable aux défaillances selon les exigences. Elle ne consiste pas à répéter toute opération jusqu’au succès apparent.

AWS montre comment les attentes prolongées occupent des ressources et comment les retries peuvent amplifier une surcharge. Les timeouts, le backoff et le jitter doivent être conçus ensemble. [1]

## 2. Construire un budget de temps

Le parcours possède un délai global. Chaque dépendance ne peut pas consommer ce délai entier, puis ouvrir ses propres reprises sans limite.

Il faut distinguer connexion, attente de réponse et durée totale, vérifier ce que couvre le client et considérer les nouveaux échanges TLS ou DNS. Des seuils trop courts créent des faux incidents ; des seuils trop longs peuvent épuiser connexions et ressources. [1]

L’échéance métier et le timeout technique restent distincts. Arrêter d’attendre ne prouve pas l’arrêt de l’exécution distante.

## 3. Définir une politique de reprise explicite

| Élément | Décision à établir |
|---|---|
| Cause | Erreur réellement transitoire ou refus durable |
| Effet | Répétition sûre, reconnaissance ou clarification nécessaire |
| Budget | Nombre de tentatives et durée totale |
| Espacement | Backoff borné et dispersion |
| Issue | État durable et responsable de la suite |

Le jitter disperse les tentatives pour éviter que tous les clients reprennent ensemble. Plusieurs couches de retries peuvent multiplier la charge plutôt que simplement l’ajouter. AWS recommande de choisir un point approprié dans la chaîne et de limiter les reprises. [1]

Pour un travail long, conserver une prochaine date de tentative peut être préférable à immobiliser un thread pendant l’attente. La concurrence sur ces travaux doit cependant être contrôlée.

## 4. Isoler les ressources

Nous proposons des limites distinctes pour consultation, transmission et investigation. Une panne partenaire ne doit pas consommer toutes les connexions disponibles pour lire les faits déjà connus.

Ces limites peuvent concerner pools, concurrence, files et admission. Un circuit breaker peut suspendre certains appels puis autoriser des sondes contrôlées ; il introduit aussi des états et des conditions de retour à tester. AWS souligne ces coûts de comportement. [1]

Aucun de ces mécanismes ne remplace l’idempotence ou une procédure de résolution.

## 5. Rester honnête en mode dégradé

Une consultation peut afficher une connaissance ancienne avec sa portée. Elle ne doit pas inventer une confirmation pour préserver une apparence de disponibilité.

Les demandes non reçues durablement, les demandes prises en charge et les résultats inconnus doivent rester distincts. Le retour à la normale doit disposer d’une capacité de rattrapage et d’une politique empêchant une nouvelle surcharge.

## 6. Exercice : l’incident se prolonge après réparation

Chaque client reprend immédiatement et chaque service recommence trois fois. Le partenaire rétabli reste saturé.

La correction supprime l’amplification entre couches, borne les budgets, disperse et limite le rattrapage. Elle prouve que les consultations essentielles restent utilisables et que les effets inconnus ne sont pas resoumis aveuglément.

## Référence

[1] AWS Builders’ Library, *Timeouts, retries, and backoff with jitter* : https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/

[Précédent](chapitre-10.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-12.md)
