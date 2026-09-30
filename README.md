# Timer · Harmonie Games

Chrono de sessions de création, par projet. S'installe sur iPhone depuis Safari, et enregistre les données dans un fichier JSON d'un dépôt GitHub privé.

## Ce que fait la V1

- Plusieurs projets en parallèle, chacun avec une discipline choisie dans une liste : Figurine, Jeu, Pixel art ou Autre. Elle se change depuis la fiche projet.
- Choisir un projet → **Démarrer** → aller filmer → revenir → **Terminer la session**.
  Le chrono enregistre l'heure de départ : tu peux quitter l'app, verrouiller le téléphone ou même la fermer, le temps restera juste.
- Une note facultative à la fin de chaque session.
- Fiche projet : nombre de sessions, temps total, moyenne par session, dates.
- **Marquer comme terminé** : l'œuvre passe dans « Terminés » avec son bilan (ex. « 9 sessions · 11 h 40 »).
- Ajouter une session oubliée, corriger ou supprimer une session.
- **Idées** : dans la fiche projet, choisir de 1 à 5 puis **Générer** propose des sujets au hasard selon la discipline (des idées générales pour « Autre »). **Garder** enregistre une idée dans le projet, **×** la retire. Tout se passe sur le téléphone, sans réseau ni service externe.
- Hors ligne : tout reste sur le téléphone et part sur GitHub au retour du réseau.

## Installation (environ 10 minutes, une seule fois)

Il faut **deux dépôts** :

| Dépôt | Visibilité | Contenu |
|---|---|---|
| `timer` | Public | L'app (ces fichiers). Public parce que GitHub Pages gratuit ne publie que les dépôts publics. Le code ne contient aucun secret. |
| `timer-data` | **Privé** | Tes données (`data.json`), créées automatiquement par l'app. |

### 1. Publier l'app

1. Sur GitHub, crée un dépôt **public** nommé `timer`.
2. Dépose-y les fichiers de ce dossier : `index.html`, `sw.js`, `manifest.webmanifest`, `icon-180.png`, `icon-192.png`, `icon-512.png`.
   (Bouton **Add file → Upload files**, puis **Commit changes**.)
3. Va dans **Settings → Pages**. Sous *Build and deployment*, choisis **Deploy from a branch**, branche `main`, dossier `/ (root)`, puis **Save**.
4. Au bout d'une minute, l'app est en ligne sur `https://<ton-compte>.github.io/timer/`.

### 2. Créer le dépôt de données

1. Crée un dépôt **privé** nommé `timer-data`.
2. Coche **Add a README file** : un dépôt vide ne peut pas recevoir de fichier par l'API.

### 3. Créer le jeton d'accès

1. GitHub → photo de profil → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
2. **Token name** : `Timer iPhone`.
3. **Expiration** : 1 an maximum (note la date quelque part, il faudra le renouveler).
4. **Repository access** : *Only select repositories* → `timer-data` uniquement.
5. **Permissions → Repository permissions → Contents** : **Read and write**. Ne touche à rien d'autre.
6. **Generate token**, puis copie le jeton (`github_pat_…`). Il n'est affiché qu'une fois.

Ce jeton ne donne accès qu'au contenu de `timer-data` : même s'il fuitait, il ne permettrait pas de toucher à tes autres dépôts.

### 4. Installer sur l'iPhone

1. Ouvre `https://<ton-compte>.github.io/timer/` dans **Safari** (pas Chrome : seul Safari peut ajouter à l'écran d'accueil de façon fiable).
2. Bouton **Partager** → **Sur l'écran d'accueil** → **Ajouter**.
3. Ouvre l'app **depuis l'icône** et va dans **Réglages** :
   - Compte : ton nom d'utilisateur GitHub
   - Dépôt : `timer-data`
   - Fichier : `data.json`
   - Jeton : colle le jeton
4. **Enregistrer et synchroniser**. Le statut en haut à droite doit passer à « Synchronisé ».

> Important : l'app installée et Safari ne partagent pas leurs données sur iPhone. Configure les réglages **dans l'app ouverte depuis l'icône**, pas dans l'onglet Safari.

Tu peux faire pareil sur ton PC ou ton Mac (même adresse, mêmes réglages) : les appareils fusionnent leurs données.

## Au quotidien

- Le point en haut à droite indique l'état : **Synchronisé**, **Synchro…**, **Hors ligne** ou **Erreur**. Touche-le pour forcer une synchronisation.
- Chaque modification crée un commit dans `timer-data` : l'onglet *Commits* de ce dépôt est ton historique de sauvegarde. Pour revenir en arrière, il suffit de restaurer une ancienne version de `data.json`.
- **Réglages → Exporter le JSON** télécharge une copie de tes données.

## Mettre à jour l'app

Remplace `index.html` (ou les autres fichiers) dans le dépôt `timer`. L'app récupère la nouvelle version à la prochaine ouverture avec du réseau. Si tu modifies `sw.js`, change aussi `timer-v1` en `timer-v2` au début du fichier.

## Si quelque chose ne marche pas

| Message | Cause probable |
|---|---|
| Jeton invalide ou expiré | Jeton mal collé ou arrivé à expiration : crée-en un nouveau (étape 3). |
| Dépôt introuvable, ou le jeton n'y a pas accès | Faute de frappe dans Compte ou Dépôt, ou `timer-data` pas sélectionné dans le jeton. |
| Écriture refusée | La permission Contents est en lecture seule : repasse-la en *Read and write*. |
| Hors ligne | Pas de réseau : rien n'est perdu, l'envoi se fera au retour de la connexion. |

## Format des données

```json
{
  "version": 1,
  "projects": [
    { "id": "…", "name": "Figurine Chopper", "discipline": "Figurine | Jeu | Pixel art | Autre",
      "status": "active | done", "createdAt": 0, "finishedAt": null, "updatedAt": 0,
      "ideas": [ { "id": "…", "text": "Un nain forgeron dans une forge", "createdAt": 0 } ] }
  ],
  "sessions": [
    { "id": "…", "projectId": "…", "start": 0, "end": 0, "note": "", "createdAt": 0, "updatedAt": 0 }
  ],
  "running": { "projectId": null, "start": null, "updatedAt": 0 }
}
```

Les dates sont en millisecondes (timestamp Unix). Les éléments supprimés restent dans le fichier avec `"deleted": true`, pour que la suppression se propage aux autres appareils. Ce format simple pourra être lu plus tard depuis Unity ou un script.
