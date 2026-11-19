# TP 6 - Réponses

## Q1

La variable n'est pas disponible dans le step qui l'a définie car `GITHUB_ENV` est un
fichier que le runner ne lit qu'à la fin du step. Les variables qu'il contient sont
injectées dans l'environnement des steps suivants, mais le shell du step en cours a
déjà été lancé avec son environnement figé.

## Q2

Dans le job `verify-env`, `$APP_NAME` est vide. Chaque job s'exécute sur un runner
distinct (une machine virtuelle neuve), qui ne partage ni fichiers ni variables avec
les autres. La portée de `GITHUB_ENV` se limite donc aux steps suivants du même job.
