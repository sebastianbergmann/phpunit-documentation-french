**The PHPUnit project is no longer involved with the translation of its documentation to other languages. This repository is now archived.**

# Documentation de PHPUnit en français

Ce dépôt contient la documentation française de [PHPUnit](https://phpunit.de/).  
La documentation est accessible à l'adresse https://phpunit.readthedocs.io/fr/latest/.

La documentation originale en anglais : https://github.com/sebastianbergmann/phpunit-documentation-english

## Traductions

Chaque traduction de la documentation est maintenue dans un dépôt
dédié:

* [Documentation en anglais](https://github.com/sebastianbergmann/phpunit-documentation-english)
* [Documentation en espagnol](https://github.com/sebastianbergmann/phpunit-documentation-spanish)
* [Documentation en français](https://github.com/sebastianbergmann/phpunit-documentation-french)
* [Documentation en brésilien et portuguais](https://github.com/sebastianbergmann/phpunit-documentation-brazilian-portuguese)
* [Documentation en japonais](https://github.com/sebastianbergmann/phpunit-documentation-japanese)
* [Documentation en chinois simplifié](https://github.com/sebastianbergmann/phpunit-documentation-chinese)
* [Documentation en russe](https://github.com/sebastianbergmann/phpunit-documentation-russian)

### Ajouter une nouvelle traduction

Si vous souhaitez créer une nouvelle traduction, merci d'ouvrir une issue sur le
tracker de la documentation anglaise, indiquant la langue que vous souhaitez
traduire. Un dépôt sera créé et ajouté aux traductions disponibles.

Idéalement, vous aurez déjà préparé une première version basée sur un fork ou
une copie de la documentation anglaise qui sera ensuite importée dans le
dépôt officiel.

## Construire la documentation

### Pré-requis

- Python
- [Sphinx](http://www.sphinx-doc.org/)
- [Read the Docs Sphinx Theme](https://github.com/rtfd/sphinx_rtd_theme)

### Générer la documentation HTML

Pour générer la documentation, exécuter la ligne de commande suivante:

```
$ make html
```

Ensuite, vous trouverez les fichiers HTML dans `build/html`.

### Relecture automatisée

####  Installation
```
$ pip install docutils-ast-writer
$ npm install
```
#### Utilisation
```
$ ./node_modules/.bin/textlint src
```

## Contribuer

Merci de prendre du temps pour aider.

### Contributions attendues

 * Mettre à jour la documentation suite aux améliorations de la [documentation anglaise](https://github.com/sebastianbergmann/phpunit-documentation-english)
 * S'il s'agit d'une mise à jour, préciser le commit de la documentation originale avec lequel la synchronisation est faite
 * Corriger les typos, les fautes d'orthographes, de grammaire et de syntaxes
 * Détecter le franglais et les anglicismes
 * Revue de pair sur les PR

### Mode d'emploi

 * Vérifier le guide de traduction
 * Faire une Pull requests documentée vers master (idéalement depuis une "feature branch").
 * Vérifier que la compilation passe avant de faire une pull request
 * Laisser les exemples de code en anglais
 * Respecter les sauts de ligne de la documentation originale afin de faciliter les recherches et les comparaisons (les numéros de lignes entre la documentation anglaise et française doivent être les mêmes)

## Guide de traduction

Dans le tableau suivant sont recensées les règles de traductions utilisées de manière à garantir la cohérence d'ensemble.
Sont notamment visés les termes techniques.

| Anglais       | Français                                                                  |
| :------------ | :------------------------------------------------------------------------ |
| actual        | constatée (valeur)                                                        |
| array         | traduit par tableau sauf quand on fait explicitement référence à PHP      |
| assertion     | traduit par asssertion, plus parlant que affirmation                      |
| composable    | composable (rien trouvé de mieux)                                         |
| expected      | attendue (valeur)                                                         |
| framework     | framework                                                                 |
| isolated      | indépendant (isolé est ambigu, étanche un peu moins...)                   |
| notice        | remarque                                                                  |
| return        | retourne plutôt que renvoie                                               |
| requirements  | pré-requis                                                                |
| extension     | extensions                                                                |
| code coverage | couverture de code                                                        |
| appendix      | annexe                                                                    |
| fixture       | fixture (pas trouvé mieux en français)                                    |
| stub          | bouchon                                                                   |
| verbe ing     | traduits par l'infinitif dans les titres : testing => tester              |


Lectures recommandées :

 - https://framacalc.org/jargon-francais
 - https://www.24joursdeweb.fr/2017/on-dit-numerique-et-pas-digital-bordel/
