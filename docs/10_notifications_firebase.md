## Notifications Firebase (FCM) — V1

### Objectif
Notifier rapidement les utilisateurs (surtout les vendeurs) lors d’événements importants :
- annonce masquée / supprimée par modération
- avertissement / suspension de compte vendeur
- annonce signalée (optionnel)
- nouveautés d’un vendeur suivi (abonnements)

### Principe
1. Le mobile récupère un **token FCM**.
2. Le mobile appelle l’API pour **enregistrer** ce token.
3. L’API envoie une notification via **FCM** et conserve un historique en base.

### Endpoints (inspirés de `akoeleapirest`)
- `POST /notifications/register-token` : enregistrer/mettre à jour un token
- `DELETE /notifications/register-token?token=...` : désinscrire un token
- `GET /notifications` : lister mes notifications (paginé)
- `GET /notifications/unread-count` : compteur non lu
- `POST /notifications/:id/read` : marquer comme lue
- `POST /notifications/read-all` : tout marquer comme lu

### Abonnements (follow) — endpoints recommandés (V1)
- `POST /sellers/:sellerId/follow`
- `DELETE /sellers/:sellerId/follow`
- `GET /me/follows` (liste des vendeurs suivis)

### Événements push “abonnés” (V1)
- **Nouvelle annonce publiée** par un vendeur suivi
- Promotions aux abonnés : V1.1/V2 (optionnel)

> Recommandation V1 : plafonner le volume (anti-spam), ex: max 3 notifications/jour/vendeur.

### Données (DB)
- `push_tokens` : tokens FCM par utilisateur
- `notifications` : type, message, statut d’envoi, read_at, erreurs

### Configuration (variables d’environnement)
Mode recommandé (FCM HTTP v1) :
- `FCM_USE_V1=true`
- `GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json`

Alternative (legacy, à éviter si possible) :
- `FCM_SERVER_KEY=...`


