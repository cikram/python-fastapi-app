# Security notes - TP 7

## Observations sur actions/setup-node

- Badge de créateur vérifié : présent. GitHub a vérifié le créateur comme une organisation partenaire officielle (organisation `actions`).
- Nombre d'étoiles : environ 5 000.
- Dossier contenant le code source : `src/` (code TypeScript). Le dossier `dist/` contient la version compilée qui est réellement exécutée.

## Q3. Pourquoi faut-il épingler une version (@v4) plutôt que @main ?

La référence placée après le `@` indique à GitHub quelle version du code de l'action télécharger au moment de l'exécution. `@main` désigne la branche de développement de l'auteur : son contenu change à chaque nouveau commit, sans que mon workflow change d'un seul caractère. Mon pipeline peut donc se mettre à se comporter différemment d'un jour à l'autre, à cause d'un bug, d'un changement non publié ou d'une modification non relue. Si le compte de l'auteur est compromis, un commit malveillant sur `main` serait exécuté dans mon workflow, avec accès au dépôt et éventuellement aux secrets. Épingler une version (`@v4`) garantit un comportement stable et testé. Un tag peut toutefois encore être déplacé par l'auteur, donc le plus sûr reste d'épingler sur le SHA complet d'un commit, qui est immuable.

## Q4. Le badge « créateur vérifié » garantit-il qu'une action est sûre ?

Non. Le badge indique que GitHub a vérifié l'identité de l'organisation qui publie l'action. Il ne certifie ni la qualité du code, ni l'absence de vulnérabilité, ni la sécurité de chaque version. Un créateur vérifié peut publier une version qui contient un bug, ou voir son compte ou sa chaîne de publication compromis. On l'a d'ailleurs observé sur le dépôt de `actions/setup-node`, dont le badge de statut « Basic validation » était en échec au moment de la consultation, alors que le créateur est vérifié. Le badge est un indice de confiance, qui ne dispense pas de relire le code, d'épingler la version et de limiter les permissions.

## Q5. Deux vérifications à faire avant d'utiliser en production une action d'un fournisseur inconnu

1. Relire le code source de l'action (le fichier `action.yml` et le dossier `src/`) pour vérifier ce qu'elle fait avec le réseau, les secrets et le `GITHUB_TOKEN`, puis l'épingler sur le SHA d'un commit précis que l'on a relu plutôt que sur une branche ou un tag mobile.
2. Évaluer la fiabilité de l'auteur et la maintenance du projet : identité de l'auteur ou de l'organisation, nombre d'étoiles et de contributeurs, date du dernier commit, issues et correctifs de sécurité en cours. En complément, limiter les permissions du `GITHUB_TOKEN` du workflow au strict nécessaire (par exemple `contents: read`).
