# Structure du bac à sable

Dépôt de test (`projet-zero-academy/bac-a-sable`) pour rôder, de bout en bout, le travail de Prométhée (OpenClaw). Il ne contient aucun code du jeu.

## Branches

| Branche | Rôle | Qui y écrit |
|---|---|---|
| `main` | Production. Jamais touchée par la chaîne automatique. | Un humain, par fusion d'une demande de fusion relue. |
| `staging` | Branche par défaut du dépôt et point d'intégration. Toute demande de fusion de Prométhée vise `staging`. | La chaîne `chaine-staging`, après les contrôles. |
| `openclaw/<nom>` | Branche de travail d'une demande, tirée de `staging`, une par sujet. Supprimée après fusion. | Prométhée, depuis sa copie de travail (worktree). |

## Chaîne automatique vers staging

1. Prométhée travaille sur `openclaw/<nom>` dans sa copie de travail, puis la demande de fusion vers `staging` est ouverte (compte `promethee-pz`).
2. Le circuit GitHub `chaine-staging` (`.github/workflows/chaine-staging.yml`) se déclenche à l'ouverture, la réouverture, la mise à jour ou le passage en « prête » d'une demande de fusion visant `staging` :
   - **`controles`** : vérifie qu'aucun fichier ajouté ou modifié n'est vide ;
   - **`fusion`** : seulement si la demande vient de `promethee-pz` et d'une branche de ce dépôt (pas d'une copie extérieure) — passe la demande en « prête », puis la fusionne avec écrasement (co-auteurs conservés) et supprime la branche ;
   - **`deploiement-staging`** : dans l'environnement GitHub `staging`, récupère `staging` et inscrit le commit déployé dans le résumé du circuit. Pour le bac à sable, ce déploiement est **simulé**.
3. Le passage de `staging` à `main` reste manuel : relecture et fusion par un humain.

Une demande de fusion ouverte par un autre compte passe par `controles` mais n'est pas fusionnée automatiquement.

## Rôle de chaque fichier

| Fichier | Rôle |
|---|---|
| `README.md` | Présentation courte du dépôt. |
| `TEST-bout-en-bout.md` | Trace du premier essai de bout en bout (création par Prométhée, publication par Sylvain avec « Publish PR »). |
| `docs/STRUCTURE.md` | Ce document : branches, chaîne vers staging, rôle des fichiers. |
| `docs/CONTACT.md` | Qui administre le projet. |
| `docs/FAQ.md` | Questions fréquentes sur le bac à sable. |
| `.github/workflows/chaine-staging.yml` | Circuit GitHub Actions de la chaîne automatique vers `staging`, copié tel quel du modèle posé par l'Atelier. |
