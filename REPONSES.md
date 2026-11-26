# TP 8 - Réponses

## Q1

La matrice génère 6 jobs : 2 systèmes (`ubuntu-latest`, `windows-latest`) multipliés par
3 versions de Python (`3.11`, `3.12`, `3.13`). Combinaisons :

- ubuntu-latest + 3.11
- ubuntu-latest + 3.12
- ubuntu-latest + 3.13
- windows-latest + 3.11
- windows-latest + 3.12
- windows-latest + 3.13

## Q2

Il y a toujours 6 jobs au total. `exclude` s'applique d'abord et retire
windows-latest + 3.11 (6 - 1 = 5 jobs). `include` ajoute ensuite ubuntu-latest + 3.14,
qui n'existe pas dans la matrice de base, ce qui crée un nouveau job (5 + 1 = 6 jobs).

## Q3

Rien ne se passerait : le job ubuntu-latest + 3.14 serait quand même exécuté.
`exclude` ne s'applique qu'aux combinaisons générées par la matrice de base
(`os` x `python-version`), et la version 3.14 n'en fait pas partie. Elle est ajoutée
par `include`, qui est traité après `exclude`. Il n'y a donc aucune combinaison à
retirer au moment où `exclude` est évalué.

## Q4

Avec `fail-fast: true`, le job windows-latest + 3.13 a échoué (`test_break` échoue sur
`win32`) et GitHub a annulé le job windows-latest + 3.12, encore en cours. Résultat :
4 jobs réussis (tous les jobs Ubuntu), 1 échoué, 1 annulé, pour une durée de 22 s.

Avec `fail-fast: false`, aucun job n'est annulé : les deux jobs Windows (3.12 et 3.13)
vont jusqu'au bout et échouent tous les deux, les 4 jobs Ubuntu restent verts.
Résultat : 4 réussis, 2 échoués, 0 annulé, pour une durée de 45 s.

La différence principale est la complétude du résultat. Avec `true`, le job annulé n'a
pas de verdict et on ne voit pas que tout Windows est touché. Avec `false`, on obtient
le tableau complet des combinaisons en échec.

Je choisirais `true` quand un seul échec rend de toute façon le pipeline inutilisable et
que l'on veut un retour rapide en économisant du temps de calcul (grosse matrice, tout
doit passer). Je choisirais `false` pour une matrice de compatibilité comme ici, afin
d'identifier si le problème vient d'un système ou d'une version précise.

## Q5

Première raison : respecter les limites de concurrence et maîtriser les coûts. Les
runners hébergés ont un nombre de jobs simultanés limité selon le forfait, et les
minutes sont facturées (davantage pour Windows). Limiter le parallélisme évite de
saturer le quota de l'organisation et de bloquer les autres pipelines.

Deuxième raison : protéger les ressources partagées sollicitées par les tests, comme une
base de données de test, un environnement de recette ou une API externe avec limite de
requêtes. Trop de jobs en parallèle peuvent provoquer des conflits, des échecs
aléatoires ou des dépassements de quota.

