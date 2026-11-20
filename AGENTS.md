# Règles pour les agents de codage : intégration Stripe

Ces règles s'appliquent à tout agent qui écrit ou modifie du code lié aux paiements Stripe. Lis-les en entier avant de commencer.

## Environnements et clés

- Développe et teste uniquement en **mode test** (`sk_test_...`, `pk_test_...`). N'utilise jamais une clé `sk_live_...` dans le code, les tests ou la configuration locale.
- Ne code jamais de clé secrète en dur. Lis-les depuis des variables d'environnement ou un gestionnaire de secrets.
- Ne committe jamais de fichier contenant une clé, même en mode test. Vérifie `.gitignore` avant chaque commit.
- Pour les cartes de test, utilise les numéros officiels de Stripe (par exemple `4242 4242 4242 4242`), jamais de vrais numéros de carte.

## Paiements et confirmation

- Ne considère jamais un paiement comme réussi à partir de la réponse du navigateur ou du front-end. Confirme-le côté serveur, idéalement via un **webhook** signé dont tu vérifies la signature.
- Utilise des **clés d'idempotence** pour les requêtes de création et de paiement, afin d'éviter les doubles facturations lors d'un nouvel essai.
- Ne stocke jamais de données de carte bancaire. Utilise Stripe Elements ou Checkout pour que les données transitent directement par Stripe.
- Gère les erreurs Stripe de façon explicite (`StripeCardError`, `StripeRateLimitError`, etc.) au lieu de les avaler silencieusement.

## Méthode de travail

- Avant d'implémenter une fonctionnalité de paiement, consulte les bonnes pratiques Stripe actuelles et vérifie la version de l'API ou du SDK utilisée. Ne te fie pas à ta mémoire pour les noms de paramètres ou les endpoints.
- Ajoute des tests pour chaque flux de paiement : succès, échec, paiement en attente et webhook reçu deux fois.
- Signale dans ta réponse toute décision qui touche l'argent réel, les remboursements ou les données clients, et demande une validation humaine avant de la mettre en production.

## Ce qu'il ne faut jamais faire

- Passer une clé live dans une variable exposée côté client.
- Désactiver la vérification de signature des webhooks.
- Contourner une erreur Stripe en modifiant le montant ou la devise côté client.
- Lancer une opération destructive (remboursement, annulation d'abonnement, suppression de client) sans demande explicite.
