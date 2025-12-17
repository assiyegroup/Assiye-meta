## Supabase Storage — Policies pour `assiye-media` (V1)

### Objectif V1 recommandé
- **Upload** : uniquement via l’API (serveur) avec `SUPABASE_SERVICE_ROLE_KEY` (le service role **bypass** les policies).
- **Lecture** : publique (tout le monde peut afficher les images des annonces) via une policy `SELECT`.

> Avec ce modèle, tu n’as pas besoin de policy `INSERT/UPDATE/DELETE` côté clients.

---

## Étape 0 — Vérifier le `bucket_id`
Dans le dashboard Supabase, le bucket affiché semble être **`ASSIYE-MEDIA`** (attention : `bucket_id` peut être sensible à la casse).

➡️ **Important** : dans les policies SQL ci-dessous, remplace `assiye-media` par le vrai `bucket_id` si nécessaire (ex: `ASSIYE-MEDIA`).

---

## Option A (recommandée V1) — lecture publique, écriture via API

### Policy 1 — Public read
À créer sur `storage.objects` :

```sql
create policy "Public read for assiye-media"
on storage.objects
for select
to anon, authenticated
using (bucket_id = 'assiye-media');
```

✅ Résultat :
- n’importe qui peut **afficher** les images (utile pour la marketplace)
- personne ne peut **uploader/modifier/supprimer** depuis le client (sauf service role via API)

---

## Option B (si tu veux permettre l’upload direct depuis l’app plus tard)
> Pas recommandé pour V1 si tu veux aller vite et garder la sécurité simple.

### Policy 1 — Public read
(identique à Option A)

### Policy 2 — Authenticated upload (direct client)

```sql
create policy "Authenticated upload to assiye-media"
on storage.objects
for insert
to authenticated
with check (bucket_id = 'assiye-media');
```

### Policy 3 — Owner can update/delete (direct client)

```sql
create policy "Owner update assiye-media objects"
on storage.objects
for update
to authenticated
using (bucket_id = 'assiye-media' and owner = auth.uid())
with check (bucket_id = 'assiye-media' and owner = auth.uid());

create policy "Owner delete assiye-media objects"
on storage.objects
for delete
to authenticated
using (bucket_id = 'assiye-media' and owner = auth.uid());
```

---

## Bonnes pratiques (structure des fichiers)
Pour éviter le désordre et faciliter la purge :
- `listings/<listing_id>/<uuid>.jpg`
- `avatars/<user_id>/<uuid>.jpg` (si besoin plus tard)


