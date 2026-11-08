# Chapitre 5 — Construire l’idempotence et traiter les doublons

## 1. Protéger l’effet, pas seulement le transport

L’idempotence signifie ici que répéter une même intention reconnue ne multiplie pas l’effet métier prévu. Les tentatives peuvent laisser plusieurs traces techniques ; elles ne doivent pas créer plusieurs paiements pour la même intention.

Un broker ne contrôle pas tous les effets produits dans les bases ou les API externes. Microsoft distingue cette limite des garanties locales et décrit le consommateur idempotent. [1]

## 2. Définir l’identité de l’intention

Dans notre contrat, une clé possède un périmètre : acteur ou organisation, opération et identité fournie. Deux paiements légitimes de même montant ne sont pas des doublons par nature.

| Entrée | Traitement proposé |
|---|---|
| Même clé, même intention canonique | Retrouver l’opération et son résultat disponible |
| Même clé, contenu incompatible | Signaler un conflit |
| Nouvelle clé pour une intention légitime | Appliquer les règles de création |

La comparaison doit préciser les champs pertinents et leur normalisation. Hacher un JSON brut peut confondre différences de présentation et différences métier. Une empreinte ne remplace pas les règles d’autorisation.

## 3. Rendre la reconnaissance atomique

Un contrôle en mémoire ou une lecture suivie d’une écriture sans arbitrage peut laisser passer deux traitements concurrents.

Nous proposons une contrainte unique sur le périmètre et la clé. La création de l’opération, l’enregistrement de sa reconnaissance et le travail durable qui en découle doivent appartenir à la même transaction lorsque le stockage le permet.

Le perdant d’un conflit doit retrouver une opération validée ou traiter explicitement son absence après rollback. Un marqueur inséré puis abandonné ne doit pas être interprété comme un succès définitif. [1]

## 4. Ne pas transformer un état en cours en garantie

Pour un effet externe, enregistrer EN_COURS avant l’appel puis TERMINE après ne supprime pas la fenêtre d’incertitude. Le partenaire peut réussir juste avant l’arrêt local.

Il faut une reconnaissance compatible chez le partenaire, une interrogation fiable ou une procédure de rapprochement. Un retry aveugle peut dupliquer l’effet ; ne jamais reprendre peut laisser le travail inachevé. AWS souligne que timeout et absence d’effet ne sont pas équivalents. [2]

## 5. Choisir une durée et un comportement de reprise

La rétention des clés doit couvrir les répétitions plausibles : clients hors ligne, messages tardifs, rejouages et restauration. Une expiration courte peut autoriser de nouveau une ancienne intention.

Le résultat retourné doit rester cohérent avec l’autorisation actuelle de consultation. La reconnaissance d’une clé ne donne pas un droit universel de lire son résultat.

Dédupliquer un message identique n’empêche pas deux messages différents de porter la même intention. Le niveau de protection doit correspondre au risque métier.

## 6. Exercice : le cache protège-t-il le paiement ?

Un cache local conserve les clés pendant quelques minutes. Deux instances reçoivent la même demande, puis redémarrent.

La correction déplace l’arbitrage vers un état durable partagé et examine concurrence, redémarrage, contenu différent et répétition tardive. Elle distingue enfin l’unicité locale de l’exécution distante.

## Références

[1] Microsoft Learn, *Idempotent Consumer pattern* : https://learn.microsoft.com/en-us/azure/architecture/patterns/idempotent-consumer

[2] AWS Builders’ Library, *Timeouts, retries, and backoff with jitter* : https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/

[Précédent](chapitre-04.md) · [Sommaire](../SOMMAIRE.md) · [Suivant](chapitre-06.md)
