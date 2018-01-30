

.. _other-uses-for-tests:

=============================
Autres utilisations des tests
=============================

Une fois que vous aurez l'habitude d'écrire des tests automatisés, vous découvrirez
certainement davantage d'usages pour les tests. En voici quelques exemples.

.. _other-uses-for-tests.agile-documentation:

Documentation agile
###################

Typiquement, dans un projet développé en utilisant un processus agile,
tel que l'Extreme Programming, la documentation ne peut pas suivre
les changements fréquents de la conception et du code du projet.
l'Extreme Programming réclame la *propriété collective du code*, donc tous
les développeurs ont besoin de savoir comment fonctionne l'intégralité du système. Si vous êtes
suffisamment discipliné pour utiliser pour vos tests des "noms parlant"
qui décrivent ce qu'une classe doit faire, vous pouvez utiliser la fonctionnalité TestDox de PHPUnit
pour générer automatiquement de la documentation pour votre projet en s'appuyant
sur ses tests. Cette documentation donne aux développeurs un aperçu de ce que chaque
classe du projet est supposée faire.

La fonctionnalité TestDox de PHPUnit examine une classe de test et tous
les noms de méthode de test pour les convertir les noms PHP au format Camel Case en phrases :
``testBalanceIsInitiallyZero()`` devient "Balance is
initially zero". S'il existe plusieurs méthodes de test dont les noms
ne diffèrent que par un suffixe constitué de un ou plusieurs chiffres, telles que
``testBalanceCannotBecomeNegative()`` et
``testBalanceCannotBecomeNegative2()``, la phrase
"Balance ne peut pas être négative" n'apparaîtra qu'une seule fois, en supposant que
tous ces tests ont réussi.

Jetons un oeil sur la documentation agile générée pour la classe
``BankAccount``

.. code-block:: bash

    $ phpunit --testdox BankAccountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    BankAccount
     [x] Balance is initially zero
     [x] Balance cannot become negative

Alternativement, la documentation agile peut être générée en HTML ou au
format texte et écrite dans un fichier en utilisant les paramètres
``--testdox-html`` et ``--testdox-text``.

La documentation agile peut être utilisée pour documenter les hypothèses
que vous faites sur les paquets externes que vous utilisez dans votre projet.
Quand vous utilisez un paquet externe, vous vous exposez au risque que le paquet
ne se comportera pas comme vous le prévoyez et que les futures versions du paquet
changeront de façon subtile, ce qui cassera votre code sans que vous ne le sachiez.
Vous pouvez adresser ces risques en écrivant un test à chaque fois que vous faites
une hypothèse. Si votre test réussit, votre hypothèse est valide. Si vous documentez
toutes vos hypothèses avec des tests, les futures livraisons du paquet externe ne
poseront pas de problème : si les tests réussissent, votre système doit continuer à
fonctionner.

.. _other-uses-for-tests.cross-team-tests:

Tests inter-équipes
###################

Quand vous documentez des hypothèses avec des tests, vous êtes
propriétaire des tests. Le fournisseur du paquet -- sur lequel vous
faites des hypothèses -- ne connaît rien de vos tests. Si vous voulez
avoir une relation plus étroite avec le fournisseur du paquet, vous
pouvez utiliser les tests pour communiquer et coordonner vos activités.

Quand vous êtes d'accord pour coordonner vos activités avec le fournisseur
d'un paquet, vous pouvez écrire les tests ensembles. Faites cela d'une telle
façon que les tests révèlent autant d'hypothèses que possible. Les hypothèses
cachées sont la mort de la coopération. Avec les tests, vous documentez exactement
ce que vous attendez du paquet fourni. Le fournisseur saura que le paquet
est prêt quand tous les tests fonctionneront.

En utilisant des bouchons (voir le chapitre relatif aux "objets mock",
précédemment dans ce livre), vous pouvez créer un découplage plus grand entre vous
et le fournisseur: le travail du fournisseur est de faire fonctionner les tests
avec l'implémentation réelle du paquet. Votre travail est de faire que les tests fonctionnent
sur votre propre code. Jusqu'à ce que vous ayez l'implémentation réelle du paquet fourni,
vous utilisez des objets bouchons. Suivant cette approche, deux équipes peuvent développer indépendamment.


