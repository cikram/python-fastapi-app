# TP 7

## Q1

L'action `actions/checkout` n'a pas besoin de `with` car tous ses paramètres ont une
valeur par défaut adaptée : elle récupère le dépôt courant, à la référence qui a
déclenché le workflow, avec le jeton fourni par GitHub. L'action `actions/setup-node`
doit en revanche savoir quelle version de Node.js installer. Aucune version par défaut
ne convient à tous les projets, il faut donc préciser `node-version` avec `with`.

## Q2

Chaque job s'exécute sur un runner distinct, qui est une machine virtuelle neuve. Le
dépôt partagé ne contient que les fichiers commités, alors que `report.txt` est créé
pendant l'exécution du job `build` et n'est jamais commité. Il n'existe donc que sur le
runner de `build`, qui est détruit à la fin du job. L'artefact permet de transférer ce
fichier d'un job à l'autre : `upload-artifact` l'envoie vers le stockage de GitHub et
`download-artifact` le récupère dans `verify`.

## Q3 à Q5

Les réponses aux questions Q3, Q4 et Q5 (sécurité des actions tierces) sont rédigées
dans le fichier `SECURITY-NOTES.md`, comme demandé dans l'énoncé.

## Q6

Une action locale est un dossier du dépôt (ici `.github/actions/build-info/` avec son
fichier `action.yml`). Le runner démarre vide : tant que `actions/checkout` n'a pas
téléchargé le dépôt, ce dossier n'existe pas sur la machine. Appeler
`uses: ./.github/actions/build-info` avant le checkout échouerait donc, car GitHub ne
trouverait pas le fichier `action.yml`. À l'inverse, une action externe comme
`setup-node` est téléchargée depuis son propre dépôt et n'a pas besoin du checkout.

## Q7

Dans une action composite, chaque step `run` doit préciser son interpréteur avec
`shell: bash`. Une action composite est conçue pour être réutilisée dans n'importe quel
workflow et sur n'importe quel système (Linux, Windows, macOS), et elle n'hérite pas du
shell par défaut défini par le workflow appelant. GitHub impose donc de déclarer
explicitement le shell pour que le comportement soit identique partout. Sans lui,
l'action est rejetée.
