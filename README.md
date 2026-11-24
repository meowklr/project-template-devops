# project-template-devops

Base DevOps prête à l'emploi pour n'importe quel dépôt GitHub, quel que soit le langage :

- **Dependabot** (`.github/dependabot.yml`) : vérifie les dépendances du projet (et les GitHub Actions) tous les lundis à 8h (`Europe/Paris`) et ouvre une Pull Request pour les correctifs et versions mineures. Les mises à jour majeures (`semver-major`) sont ignorées par défaut.
- **CI** (`.github/workflows/ci.yml`) : exécute les tests à chaque `push` ou `pull_request` vers `main` (et manuellement depuis l'onglet Actions).

## Utilisation

**Nouveau projet :** clique sur **Use this template > Create a new repository** sur GitHub, puis clone le dépôt.

**Projet existant :** copie le dossier `.github/` à la racine de ton dépôt.

## Adapter à ton langage

### 1. `.github/dependabot.yml`

Remplace `package-ecosystem: "npm"` par l'écosystème de ton projet :

| Langage / outil | Valeur `package-ecosystem` |
| :--- | :--- |
| Node.js (npm / yarn / pnpm) | `npm` |
| Python (pip / poetry / uv) | `pip` ou `uv` |
| PHP | `composer` |
| Go | `gomod` |
| Rust | `cargo` |
| Java | `maven` ou `gradle` |
| .NET / C# | `nuget` |
| Ruby | `bundler` |
| Docker | `docker` |

La liste complète des écosystèmes supportés est disponible dans la [documentation Dependabot](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file#package-ecosystem).

**Cas particuliers :**
- **Projet multi-langages ou monorepo** : duplique le bloc `- package-ecosystem:` autant de fois que nécessaire en ajustant `directory` (ex. `"/frontend"`, `"/api"`).
- **Projet sans gestionnaire de paquets** (HTML/CSS pur, scripts shell, etc.) : supprime le second bloc et garde uniquement `github-actions`.
- **Branche par défaut** : Dependabot utilise automatiquement la branche par défaut du dépôt (`main`, `master`, etc.), pas besoin de la préciser.

### 2. `.github/workflows/ci.yml`

Le workflow suit toujours le même schéma : installer le runtime, installer les dépendances, lancer les tests (ou le build).

Node.js est actif par défaut. Des blocs prêts à décommenter sont inclus pour **Python, PHP, Go, Rust, Java (Maven) et Docker**. Pour utiliser un autre langage, remplace simplement les étapes sous `Checkout` par les commandes que tu lances habituellement en local.

Points à vérifier avant de pousser :
- **Branche principale** : si ton dépôt utilise `master` au lieu de `main`, remplace `"main"` dans `on.pull_request.branches` et `on.push.branches`.
- **Node.js** : `npm ci` demande un fichier `package-lock.json` versionné et un script `test` dans `package.json`.
- **Pas de tests** : tu peux remplacer l'étape de test par une commande de build ou de lint (`npm run build`, `ruff check .`, etc.).

## Bloquer le merge si la CI échoue (optionnel)

Par défaut, GitHub affiche le résultat des tests sur les Pull Requests mais n'empêche pas de merger. Pour rendre le passage de la CI obligatoire :

> Sur un compte GitHub gratuit, les Rulesets ne s'appliquent qu'aux dépôts publics.

1. Pousse tes changements et vérifie qu'un premier run passe dans l'onglet **Actions**.
2. Va dans **Settings > Rules > Rulesets > New branch ruleset**.
3. Passe **Enforcement status** sur **Active**.
4. Dans **Target branches**, clique sur **Add target > Include default branch**.
5. Coche **Require status checks to pass**, clique sur **Add checks** et sélectionne `Verification du code`.
6. Enregistre le ruleset.

Une fois en place, les PR ouvertes par Dependabot (ou par toi) ne pourront plus être mergées tant que la CI est en échec.
