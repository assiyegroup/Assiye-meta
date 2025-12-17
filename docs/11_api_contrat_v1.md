## Contrat API — V1 (extrait : bascule “mode vendeur”)

> Convention: toutes les routes sont préfixées par `/api` (ex: `/api/seller/activate`), et utilisent un JWT `Authorization: Bearer <token>`.

### Objectif
Permettre à un **utilisateur** (compte unique) d’activer le **mode vendeur** sans créer un nouveau compte.

---

## 1) Endpoints “mode vendeur”

### 1.1 `GET /seller/me`
Retourne l’état vendeur de l’utilisateur courant (profil + complétude).

**Réponse 200**
- `is_seller_enabled`: boolean
- `seller_profile`: objet ou `null`
- `completion`:
  - `is_ready_to_activate`: boolean
  - `missing`: string[] (ex: `["display_name","whatsapp_phone","location","schedule"]`)

---

### 1.2 `PUT /seller/profile`
Met à jour les informations vendeur (créé `seller_profile` si absent).

**Body**
- `display_name` (string, requis pour activer)
- `business_type` (string, optionnel V1)
- `whatsapp_phone` (string, requis pour activer)
- `phone` (string, optionnel)
- `email` (string, optionnel)

**Réponse 200**
- `seller_profile`: objet

**Erreurs**
- `401` non authentifié
- `422` validation (ex: whatsapp invalide)

---

### 1.3 `PUT /seller/location`
Définit le **point principal** du vendeur (V1 = 1 seul).

**Body**
- `label` (string, requis pour activer)
- `address_text` (string, requis pour activer)
- `lat` (number, requis pour activer)
- `lng` (number, requis pour activer)
- `radius_m` (number, optionnel)

**Réponse 200**
- `seller_location`: objet

**Erreurs**
- `401` non authentifié
- `422` validation (lat/lng hors plage)

---

### 1.4 `PUT /seller/location/schedule`
Définit les **horaires** du point principal.

**Body**
- `timezone` (string, optionnel, défaut: `Africa/Lome`)
- `entries` (array, requis pour activer):
  - `day_of_week` (0–6)
  - `start_time` ("HH:MM")
  - `end_time` ("HH:MM")

**Règles de validation**
- `entries.length >= 1`
- `start_time < end_time`
- pas de chevauchement pour un même `day_of_week` (recommandé)

**Réponse 200**
- `schedule`: liste normalisée

---

### 1.5 `POST /seller/activate`
Active le mode vendeur **si** les informations minimales sont complètes.

**Principe**
- Endpoint **idempotent**: si déjà activé, retourne `200` sans erreur.

**Validations minimales (V1)**
- `seller_profile` existe (sinon créé vide)
- `display_name` présent
- `whatsapp_phone` présent
- `seller_location` présent (label + address + lat/lng)
- `schedule.entries.length >= 1`
- si `seller_profile.status == "suspended"` → refus

**Réponse 200**
- `is_seller_enabled`: `true`
- `seller_profile` (avec `status: "active"`)

**Réponse 422 (choix V1)**
- `message`: "SELLER_NOT_READY"
- `missing`: string[]

**Exemple 422**
- `message`: "SELLER_NOT_READY"
- `missing`: `["display_name","whatsapp_phone","location","schedule"]`

**Erreurs**
- `401` non authentifié
- `403` vendeur suspendu

---

## 2) Notes d’implémentation (NestJS)
Recommandations pour garder l’API propre :
- `SellerGuard` (ou simple check dans service) : “seller_profile actif” requis pour endpoints annonce vendeur.
- `completionService` : calcule `missing[]` et `is_ready_to_activate`.
- Quand une action admin change le statut vendeur (warn/suspend), envoyer une notif FCM (déjà prévu).


