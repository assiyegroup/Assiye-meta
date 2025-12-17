## Architecture & sécurité (proposition V1)

### Composants
- **Mobile** : Flutter (Android/iOS)
- **Admin Web** : Next.js (dashboard)
- **API** : NestJS (REST)
- **DB** : PostgreSQL
- **Stockage médias** : **Supabase Storage** (bucket) + URLs signées
- **Notifications** : **Firebase Cloud Messaging (FCM)**
- **Carte** : Google Maps ou OpenStreetMap (choix UI, données agnostiques)

### Schéma logique (haut niveau)
- Mobile/Admin → (HTTPS) → API
- API → DB (PostgreSQL)
- API → Supabase Storage (upload images)
- API → FCM (push notifications)

### Authentification (décision V1)
On **réutilise le module d’auth existant** de `akoeleapirest` (adapté au projet) :
- **Email + mot de passe + OTP** (OTP envoyé par email via SendGrid)
- **JWT** (access token)
- **Refresh token** (rotation; actuellement via cookie httpOnly)
- Optionnel : **connexion Google** (mobile)

> Adaptation recommandée pour Flutter : accepter aussi le refresh token via body/header (en plus du cookie) pour éviter les soucis de “cookie jar”.

### Autorisation (RBAC)
- Middleware/guard par rôle sur l’API.
- Les endpoints admin séparés (`/admin/...`) + vérification rôle.

### Sécurité API (minimum V1)
- HTTPS partout.
- Validation des payloads (DTO + class-validator).
- Rate limiting (surtout auth, signalements, création annonces).
- Upload images : contrôle type/poids + noms uniques + upload via Supabase Storage.
- Journalisation : erreurs + `admin_actions` pour audit.
- CORS strict pour le dashboard.

### Médias (Supabase) — recommandation V1
- Les uploads passent par l’API (serveur) avec `SUPABASE_SERVICE_ROLE_KEY` (évite d’exposer des clés côté mobile/web).
- L’API enregistre dans `listing_media` :
  - `url` (public URL ou signed URL courte durée)
  - optionnel : `storage_key` (chemin dans le bucket) pour régénérer des signed URLs.

**Bucket**
- Nom : `assiye-media`

### Notifications (Firebase/FCM) — recommandation V1
- Reprendre le module `notifications` de `akoeleapirest` :
  - `push_tokens` (enregistrement token par user)
  - `notifications` (historique + non-lus)
  - endpoints: register/unregister token, lister, unread-count, mark-read
- Mode FCM recommandé : **HTTP v1** via service account (`GOOGLE_APPLICATION_CREDENTIALS`) + `FCM_USE_V1=true`

### Stratégie “modération progressive”
- Publication directe (rapide) + outils admin + signalements + seuils.
- Possibilité d’activer une “validation préalable” plus tard si le volume/abus augmente.

### Découpage API (modules)
- Auth
- Users / Sellers
- Categories
- Regions / Cities
- Listings
- Media
- Promotions
- Reports (signalements)
- Admin (actions, dashboards)


