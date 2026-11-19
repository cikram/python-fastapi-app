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
