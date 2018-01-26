

.. _organizing-tests:

===================
Organiser les tests
===================

L'un des objectifs de PHPUnit est que les tests
soient combinables : nous voulons pouvoir exécuter n'importe quel nombre ou combinaison
de tests ensembles, par exemple tous les tests pour le projet entier, ou
les tests pour toutes les classes d'un composant qui constitue une partie du projet ou
simplement les tests d'une seule classe particulière.

PHPUnit gère différente façon d'organiser les tests et de les combiner en une
suite de tests. Ce chapitre montre les approches les plus communément utilisées.

.. _organizing-tests.filesystem:

Composer une suite de tests en utilisant le système de fichiers
###############################################################

La façon probablement la plus simple d'organiser une suite de tests est de mettre
tous les fichiers sources des cas de test dans un répertoire de tests. PHPUnit peut
automatiquement trouver et exécuter les tests en parcourant récursivement le répertoire test.

Jetons un oeil à la suite de tests de la bibliothèque
`sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_.
En regardant la structure des répertoires du projet, nous voyons que
les classes des cas de test dans le répertoire :file:`tests` reflètent la structure des
paquetages et des classes du système en cours de test (SCT, System Under Test ou SUT) dans le répertoire
:file:`src` :

.. code-block:: bash

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

Pour exécuter tous les tests de la bibliothèque, nous n'avons qu'à faire
pointer le lanceur de tests en ligne de commandes de PHPUnit sur ce répertoire test :

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Note

   Si vous pointez le lanceur de tests en ligne de commandes de PHPUnit sur
   un répertoire, il va chercher les fichiers :file:`*Test.php`.

Pour n'exécuter que les tests déclarés dans la classe de cas de test
``CurrencyTest`` dans :file:`tests/CurrencyTest`,
nous pouvons utiliser la commande suivante :

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

Pour un contrôle plus fin sur les tests à exécuter, nous pouvons utiliser
l'option ``--filter`` :

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Note

   Un inconvénient de cette approche est que nous n'avons pas de contrôle sur
   l'ordre dans lequel les tests sont exécutés. Ceci peut conduire à des problèmes
   concernant les dépendances des tests, voir :ref:`writing-tests-for-phpunit.test-dependencies`.
   Dans la prochaine section, nous verrons comment nous pouvons rendre
   l'ordre d'exécution des tests explicite en utilisant le fichier de configuration XML.

.. _organizing-tests.xml-configuration:

Composer une suite de tests en utilisant la configuration XML
#############################################################

Le fichier de configuration XML de PHPUnit (:ref:`appendixes.configuration`)
peut aussi être utilisé pour composer une suite de tests.
:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`
montre un exemple minimaliste d'un fichier :file:`phpunit.xml` qui va ajouter toutes les classes
``*Test`` trouvées dans les fichiers
:file:`*Test.php` quand :file:`tests`
est parcouru récursivement.

.. code-block:: php
    :caption: Composer une suite de tests en utilisant la configuration XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

Si un fichier :file:`phpunit.xml` ou
:file:`phpunit.xml.dist` (dans cet ordre) existe dans
le répertoire de travail courant et que l'option ``--configuration`` n'est
*pas* utilisée, la configuration sera automatiquement
lue depuis ce fichier.

L'ordre dans lequel les tests sont exécutés peut être rendu explicite :

.. code-block:: php
    :caption: Composer une suite de tests en utilisant la configuration XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>


