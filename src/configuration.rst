

.. _appendixes.configuration:

===============================
Le fichier de configuration XML
===============================

.. _appendixes.configuration.phpunit:

PHPUnit
#######

Les attributs d'un élément ``<phpunit>`` peuvent être
utilisés pour configurer les fonctionnalités du coeur de PHPUnit.

.. code-block:: bash

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/6.3/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             printerClass="PHPUnit\TextUI\ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit\Runner\StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

Le fichier de configuration XML ci-dessus correspond au comportement par
défaut du lanceur de tests TextUI documenté dans :ref:`textui.clioptions`.

Des options supplémentaires qui ne sont pas disponibles en tant qu'option de ligne de commandes sont :

``convertErrorsToExceptions``

    Par défaut, PHPUnit va installer un gestionnaire d'erreur qui converti
    les erreurs suivantes en exceptions :

    - ``E_WARNING``

    - ``E_NOTICE``

    - ``E_USER_ERROR``

    - ``E_USER_WARNING``

    - ``E_USER_NOTICE``

    - ``E_STRICT``

    - ``E_RECOVERABLE_ERROR``

    - ``E_DEPRECATED``

    - ``E_USER_DEPRECATED``

    Mettre ``convertErrorsToExceptions`` à
    ``false`` désactivera cette fonctionnalité.

``convertNoticesToExceptions``

    Si défini à ``false``, le gestionnaire d'erreurs installé
    par ``convertErrorsToExceptions`` ne convertira pas les erreurs
    ``E_NOTICE``, ``E_USER_NOTICE``, ou
    ``E_STRICT`` en exceptions.

``convertWarningsToExceptions``

    Si défini à ``false``, le gestionnaire d'erreurs installé
    par ``convertErrorsToExceptions`` ne convertira pas les erreurs
    ``E_WARNING`` ou ``E_USER_WARNING``
    en exceptions.

``forceCoversAnnotation``

    La couverture de code ne sera enregistrée que pour les tests qui
    utilisent l'annotation ``@covers`` documentée dans
    :ref:`appendixes.annotations.covers`.

``timeoutForLargeTests``

    Si les limites de temps basées sur la taille du test sont appliquées alors cet attribut
    défini le délai pour tous les tests marqués comme ``@large``.
    Si un test ne se termine pas dans le délai configuré, il
    échouera.

``timeoutForMediumTests``

    Si les limites de temps basées sur la taille du test sont appliquées alors cet attribut
    défini le délai pour tous les tests marqués comme ``@medium``.
    Si un test ne se termine pas dans le délai configuré, il
    échouera.

``timeoutForSmallTests``

    Si les limites de temps basées sur la taille du test sont appliquées alors cet attribut
    défini le délai pour tous les tests qui ne sont pas marqués comme
    ``@medium`` or ``@large``. Si un test
    ne se termine pas dans le délai configuré, il échouera.

.. _appendixes.configuration.testsuites:

Série de tests
##############

L'élément ``<testsuites>`` et son ou ses
enfants ``<testsuite>`` peuvent être utilisés pour
composer une série de tests à partir des séries de test et des cas de test.

.. code-block:: bash

    <testsuites>
      <testsuite name="My Test Suite">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

En utilisant les attributs ``phpVersion`` et
``phpVersionOperator``, une version requise de PHP
peut être indiquée. L'exemple ci-dessous ne va ajouter que
les fichiers :file:`/path/to/\*Test.php` et
:file:`/path/to/MyTest.php` si la version de PHP est
au moins 5.3.0.

.. code-block:: bash

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

L'attribut ``phpVersionOperator`` est facultatif et vaut par
défaut ``>=``.

.. _appendixes.configuration.groups:

Groupes
#######

L'élément ``<groups>`` et ses enfants
``<include>``,
``<exclude>`` et
``<group>`` peuvent être utilisés pour choisir
des groupes de tests marqués avec l'annotation ``@group``
(documenté dans :ref:`appendixes.annotations.group`)
qui doivent (ou ne doivent pas) être exécutés.

.. code-block:: bash

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

La configuration XML ci-dessus revient à appeler le lanceur de test TextUI
avec les options suivantes:

-

  ``--group name``

-

  ``--exclude-group name``

.. _appendixes.configuration.whitelisting-files:

Inclure des fichiers de la couverture de code
#############################################

L'élément ``<filter>`` et ses enfants peuvent être
utilisés pour configurer les listes blanches pour les rapports de couverture de code.

.. code-block:: bash

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

.. _appendixes.configuration.logging:

Journalisation
##############

L'élément ``<logging>`` et ses enfants
``<log>`` peuvent être utilisés pour configurer
la journalisation de l'exécution des tests.

.. code-block:: bash

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="junit" target="/tmp/logfile.xml" logIncompleteSkipped="false"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

La configuration XML ci-dessus revient à invoquer le lanceur de tests TextUI
avec les options suivantes :

-

  ``--coverage-html /tmp/report``

-

  ``--coverage-clover /tmp/coverage.xml``

-

  ``--coverage-php /tmp/coverage.serialized``

-

  ``--coverage-text``

-

  ``> /tmp/logfile.txt``

-

  ``--log-junit /tmp/logfile.xml``

-

  ``--testdox-html /tmp/testdox.html``

-

  ``--testdox-text /tmp/testdox.txt``

Les attributs ``lowUpperBound``, ``highLowerBound``,
``logIncompleteSkipped`` et
``showUncoveredFiles`` n'ont pas d'options équivalentes pour le lanceur de
tests TextUI.

-

  ``lowUpperBound: pourcentage de couverture maximum considérée comme étant faible.``

-

  ``highLowerBound: pourcentage de couverture minimum considérée comme étant forte.``

-

  ``showUncoveredFiles: Montre tous les fichiers en liste blanche dans la sortie ``--coverage-text``  et pas seulement ceux possédant des informations de couverture.``

-

  ``showOnlySummary``: Montre seulement le résumé dans la sortie ``--coverage-text``.

.. _appendixes.configuration.test-listeners:

Écouteurs de tests
##################

L'élément ``<listeners>`` et ses enfants
``<listener>`` peuvent être utilisés pour brancher des
écouteurs de tests additionnels lors de l'exécution des tests.

.. code-block:: bash

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

La configuration XML ci-dessus revient à brancher l'objet
``$listener`` (voir ci-dessous) à l'exécution des tests :

.. code-block:: bash

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. _appendixes.configuration.extensions:

Enregistrer des extensions TestRunner
#####################################

L'élément ``<extensions>`` et ses enfants ``<extension>``
peuvent être utilisés pour enregistrer des extensions TestRunner personnalisées.

:numref:`configuration.examples.RegisterExtension` montre comment enregistrer
une telle extension.

.. code-block:: xml
    :caption: Enregistrer une extension TestRunner
    :name: configuration.examples.RegisterExtension

      <?xml version="1.0" encoding="UTF-8"?>
      <phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/7.1/phpunit.xsd">
          <extensions>
              <extension class="Vendor\MyExtension"/>
          </extensions>
      </phpunit>

.. _appendixes.configuration.php-ini-constants-variables:

Configurer les réglages de PHP INI, les constantes et les variables globales
############################################################################

L'élément ``<php>`` et ses enfants peuvent être utilisés
pour configurer les réglages PHP, les constantes et les variables globales. Il peut
également être utilisé pour préfixer l'``include_path``.

.. code-block:: bash

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

La configuration XML ci-dessus correspond au code PHP suivant :

.. code-block:: bash

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';

Par défaut, les variables d'environnement ne sont pas écrasées si elles existent déjà.
Pour forcer l'écrasement de variables existantes, utilisez l'attribut ``force`` :

.. code-block:: xml

    <php>
      <env name="foo" value="bar" force="true"/>
