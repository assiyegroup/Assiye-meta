## Modèle de données (proposition V1)

### Conventions
- IDs : UUID
- Dates : `created_at`, `updated_at`
- Soft delete optionnel : `deleted_at` si nécessaire
- Colonnes DB : `snake_case`

### Entités principales

#### users
- `id`
- `role` : `user | admin_secondary | admin_primary`
- `email` (requis en V1)
- `phone` (requis en V1, utile aussi pour WhatsApp)
- `password_hash` (V1 : email+mdp + OTP)
- `is_active` (ou équivalent)
- `created_at`, `updated_at`

> Note : un utilisateur peut être **acheteur** par défaut et activer le **mode vendeur** via `seller_profiles` (capacité, pas un second compte).

#### seller_profiles
- `id`
- `user_id` (FK users)
- `display_name`
- `business_type` (texte ou future table “professions”)
- `whatsapp_phone`
- `phone` (optionnel)
- `email` (optionnel)
- `status` : `active | warned | suspended`
- `created_at`, `updated_at`

#### seller_location (V1 = 1 point principal)
- `id`
- `seller_id` (FK seller_profiles)
- `label`
- `address_text`
- `lat`, `lng`
- `radius_m` (optionnel)
- `created_at`, `updated_at`

#### seller_location_schedule
- `id`
- `seller_location_id` (FK seller_location)
- `day_of_week` (0–6)
- `start_time` (HH:MM)
- `end_time` (HH:MM)
- `timezone` (optionnel)

#### regions
- `id`
- `name`

#### cities
- `id`
- `region_id` (FK regions)
- `name`

#### categories
- `id`
- `name`
- `parent_id` (FK categories, optionnel)
- `is_active`

#### listings (annonces)
- `id`
- `seller_id` (FK seller_profiles)
- `category_id` (FK categories)
- `title`
- `description`
- `price_amount` (optionnel)
- `price_currency` (ex: XOF)
- `price_on_request` (bool)
- `status` : `published | hidden` (+ `draft` optionnel)
- `city_id` (FK cities, optionnel si on déduit via GPS)
- `created_at`, `updated_at`

#### listing_media
- `id`
- `listing_id` (FK listings)
- `url`
- `type` (image)
- `sort_order`

#### promotions
- `id`
- `listing_id` (FK listings)
- `type` : `percentage | amount | special`
- `value` (optionnel)
- `label`
- `start_at`
- `end_at`
- `is_active`

#### push_tokens (Firebase)
- `id`
- `user_id` (FK users)
- `token` (unique)
- `platform` : `android | ios | web`
- `device_info` (json, optionnel)
- `created_at`, `updated_at`

#### notifications
- `id`
- `recipient_id` (FK users)
- `type` (enum)
- `message`
- `sent_via` : `push | sms | email`
- `sent_at`
- `read_at` (nullable)
- `delivery_status` : `pending | sent | failed`
- `attempts`
- `last_error` (nullable)

#### seller_follows (abonnements)
- `id`
- `follower_user_id` (FK users) — acheteur
- `seller_id` (FK seller_profiles) — vendeur suivi
- `created_at`

#### reports (signalements)
- `id`
- `reporter_user_id` (FK users)
- `listing_id` (FK listings)
- `reason` : enum (spam, scam, prohibited, duplicate, other)
- `comment` (optionnel)
- `status` : `open | resolved | dismissed`
- `created_at`, `updated_at`

#### admin_actions (audit)
- `id`
- `admin_user_id` (FK users)
- `action_type` (hide_listing, warn_seller, suspend_seller, etc.)
- `target_type` (listing, seller, etc.)
- `target_id`
- `note`
- `created_at`

### Relations clés
- `users 1–1 seller_profiles` (si role seller)
- `seller_profiles 1–1 seller_location` (V1)
- `seller_location 1–n seller_location_schedule`
- `seller_profiles 1–n listings`
- `listings 1–n listing_media`
- `listings 0–n promotions`
- `users 1–n push_tokens`
- `users 1–n notifications`
- `users 1–n seller_follows`
- `seller_profiles 1–n seller_follows`
- `listings 0–n reports`


