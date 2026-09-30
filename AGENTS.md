# AGENTS.md — consignes pour tout agent qui travaille sur ce dépôt

Dépôt de test de Project Zero : il sert à roder la chaîne automatique (demande → demande de fusion → `staging`). Il ne contient aucun code du jeu.

## Règles
- **Jamais `main`** : les branches partent de `staging`, les demandes de fusion visent `staging`.
- Aucun secret, aucune donnée réelle ; fichiers courts et lisibles.
- Une demande = un sujet. Demande floue ou risquée : poser la question, ne rien publier.
- Ni commit, ni push, ni fusion par l'agent : la publication et la fusion se font par la chaîne ci-dessous.

## Chaîne automatique (Prométhée, sur le serveur du projet)

Une demande qu'un humain écrit lui-même dans une session **en worktree** de ce dépôt **vaut accord de publication vers `staging`** (décision des administrateurs, 30/09/2026) : ne pas redemander « je commite ? ». Tout le processus est automatique jusqu'à `staging`, vérification comprise ; la validation humaine a lieu au bouton « Deploy production » (jamais automatique).

1. **À jour** : avant de modifier, `git fetch origin && git merge --ff-only origin/staging`.
2. **Carte Workboard** : `workboard_list` pour reprendre une carte existante du même sujet, sinon `workboard_create` (titre = la demande, étiquette `demande:<login GitHub du demandeur>`, statut `running`, et dans les notes la ligne `Session : <clé de cette session>`, donnée par `session_status`).
3. **Travail** : faire la demande et la vérifier (relecture, tests s'il y en a). Aucun commit, aucune publication par l'agent.
4. **Fin du travail** : passer la carte en `review`. C'est le signal : un script des administrateurs sur le serveur (sans IA) publie ensuite la session une fois le tour terminé (commit sous `promethee-pz`, demandeur en co-auteur, demande de fusion vers `staging`), puis commente la carte avec le lien. Le circuit `chaine-staging` contrôle, fusionne et déploie `staging`.
5. Dire au demandeur : « terminé, publication automatique dans la minute, puis vérification sur staging ».
6. **Vérification sur staging** : quand le script écrit « Vérification staging (script des administrateurs, sans IA) » dans la session, avec le commit de `staging` :
   - `git fetch origin && git checkout --detach <commit>` (pas de commit, pas de push) ;
   - tests : (a) la demande de la carte est bien faite, fichier par fichier, et rien d'autre n'a changé (`git show --stat <commit>`) ; (b) les tests du dépôt s'il y en a ; (c) l'adresse de staging répond et montre le changement, s'il y en a une (le bac à sable n'a qu'un déploiement simulé : vérifier le commit de `staging`) ;
   - **tous verts** → `workboard_proof` (statut `passed`, liste des tests, lien de la demande de fusion), puis `workboard_move` en `done` ; dire au demandeur « vérifié sur staging, carte terminée » ;
   - **un test rouge** → `workboard_comment` (quel test, pourquoi), `workboard_move` en `blocked` ; le dire au demandeur. Pas de correction dans cette session (sa branche est déjà fusionnée) : une correction = une nouvelle demande.
- Travail inachevé, question en suspens ou demande floue : **ne pas** passer la carte en `review` (sinon le script publierait).
