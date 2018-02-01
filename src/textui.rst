

.. _textui:

=========================================
Le lanceur de tests en ligne de commandes
=========================================

Le lanceur de tests en ligne de commandes de PHPUnit peut être appelé via
la commande :file:`phpunit`. Le code suivant montre comment exécuter
des tests avec le lanceur de tests en ligne de commandes de PHPUnit:

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

Lorsqu'il est appelé comme indiqué ci-dessus, le contrôleur de ligne de commande PHPUnit recherchera
un fichier source :file:`ArrayTest.php` dans le répertoire de travail
courant, le chargera et s'attendra à trouver une classe de cas de test
``ArrayTest``. Il exécutera alors les tests de cette classe.

Pour chaque test exécuté, l'outil en ligne de commandes de PHPUnit affiche un
caractère pour indiquer l'avancement:

``.``

    Affiché quand le test a réussi.

``F``

    Affiché quand une assertion échoue lors de l'exécution d'une méthode de test.

``E``

    Affiché quand une erreur survient pendant l'exécution d'une méthode de test.

``R``

    Affiché quand le test a été marqué comme risqué (voir
    :ref:`risky-tests`).

``S``

    Affiché quand le test a été sauté (voir
    :ref:`incomplete-and-skipped-tests`).

``I``

    Affiché quand le test est marqué comme incomplet ou pas encore
    implémenté (voir :ref:`incomplete-and-skipped-tests`).

PHPUnit différencie les *échecs* et les
*erreurs*. Un échec est une assertion PHPUnit violée
comme un appel en échec de ``assertEquals()``.
Une erreur est une exception inattendue ou une erreur PHP. Parfois
cette distinction s'avère utile car les erreurs tendent à être plus faciles
à corriger que les échecs. Si vous avez une longue liste de problèmes, il vaut
mieux éradiquer d'abord les erreurs pour voir s'il reste encore des échecs
une fois qu'elles ont été corrigées.

.. _textui.clioptions:

Options de la ligne de commandes
################################

Jetons un oeil aux options du lanceur de tests en ligne de commandes
dans le code suivant :

.. code-block:: bash

    $ phpunit --help
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format.
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
      --coverage-html <dir>       Generate code coverage report in HTML format.
      --coverage-php <file>       Export PHP_CodeCoverage object to file.
      --coverage-text=<file>      Generate code coverage report in text format.
                                  Default: Standard output.
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
      --whitelist <dir>           Whitelist <dir> for code coverage analysis.
      --disable-coverage-ignore   Disable annotations for ignoring code coverage.

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file.
      --log-teamcity <file>       Log test execution in TeamCity format to file.
      --testdox-html <file>       Write agile documentation in HTML format to file.
      --testdox-text <file>       Write agile documentation in Text format to file.
      --testdox-xml <file>        Write agile documentation in XML format to file.
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run.
      --testsuite <name,...>      Filter which testsuite to run.
      --group ...                 Only runs tests from the specified group(s).
      --exclude-group ...         Exclude tests from the specified group(s).
      --list-groups               List available test groups.
      --list-suites               List available test suites.
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything.
      --strict-coverage           Be strict about @covers annotation usage.
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests.
      --disallow-resource-usage   Be strict about resource usage during small tests.
      --enforce-time-limit        Enforce time limit based on test size.
      --disallow-todo-tests       Disallow @todo-annotated tests.

      --process-isolation         Run each test in a separate PHP process.
      --globals-backup            Backup and restore $GLOBALS for each test.
      --static-backup             Backup and restore static attributes for each test.

      --colors=<flag>             Use colors in output ("never", "auto" or "always").
      --columns <n>               Number of columns to use for progress output.
      --columns max               Use maximum number of columns for progress output.
      --stderr                    Write to STDERR instead of STDOUT.
      --stop-on-error             Stop execution upon first error.
      --stop-on-failure           Stop execution upon first error or failure.
      --stop-on-warning           Stop execution upon first warning.
      --stop-on-risky             Stop execution upon first risky test.
      --stop-on-skipped           Stop execution upon first skipped test.
      --stop-on-incomplete        Stop execution upon first incomplete test.
      --fail-on-warning           Treat tests with warnings as failures.
      --fail-on-risky             Treat risky tests as failures.
      -v|--verbose                Output more verbose information.
      --debug                     Display debugging information.

      --loader <loader>           TestSuiteLoader implementation to use.
      --repeat <times>            Runs the test(s) repeatedly.
      --teamcity                  Report test execution progress in TeamCity format.
      --testdox                   Report test execution progress in TestDox format.
      --testdox-group             Only include tests from the specified group(s).
      --testdox-exclude-group     Exclude tests from the specified group(s).
      --printer <printer>         TestListener implementation to use.

    Configuration Options:

      --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
      -c|--configuration <file>   Read configuration from XML file.
      --no-configuration          Ignore default configuration file (phpunit.xml).
      --no-coverage               Ignore code coverage configuration.
      --no-extensions             Do not load PHPUnit extensions.
      --include-path <path(s)>    Prepend PHP's include_path with given path(s).
      -d key[=value]              Sets a php.ini value.
      --generate-configuration    Generate configuration file with suggested settings.

    Miscellaneous Options:

      -h|--help                   Prints this usage information.
      --version                   Prints the version and exits.
      --atleast-version <min>     Checks that version is greater than min and exits.

``phpunit UnitTest``

    Exécute les tests qui sont fournis par la classe
    ``UnitTest``. Cette classe est supposée être déclarée
    dans le fichier source :file:`UnitTest.php`.

    ``UnitTest`` doit soit être une classe qui hérite
    de ``PHPUnit\Framework\TestCase`` soit une classe qui
    fournit une méthode ``public static suite()`` retournant
    un objet ``PHPUnit_Framework_Test``, par exemple
    une instance de la classe
    ``PHPUnit_Framework_TestSuite``.

``phpunit UnitTest UnitTest.php``

    Exécute les tests qui sont fournis par la classe
    ``UnitTest``. Cette classe est supposée être déclarée
    dans le fichier source indiqué.

``--coverage-clover``

    Génère un fichier de log au format XML avec les informations de couverture de code
    pour les tests exécutés. Voir :ref:`logging` pour plus de détails.

    Merci de noter que cette fonctionnalité n'est seulement disponible que
    lorsque les extensions tokenizer et Xdebug sont installées.

``--coverage-crap4j``

    Génère un rapport de couverture de code au format Crap4j. Voir
    :ref:`code-coverage-analysis` pour plus de détails.

    Merci de noter que cette fonctionnalité n'est seulement disponible que
    lorsque les extensions tokenizer et Xdebug sont installées.

``--coverage-html``

    Génère un rapport de couverture de code au format HTML. Voir
    :ref:`code-coverage-analysis` pour plus de détails.

    Merci de noter que cette fonctionnalité n'est seulement disponible que
    lorsque les extensions tokenizer et Xdebug sont installées.

``--coverage-php``

    Génère un objet sérialisé PHP_CodeCoverage contenant les
    informations de couverture de code.

    Merci de noter que cette fonctionnalité n'est seulement disponible que
    lorsque les extensions tokenizer et Xdebug sont installées.

``--coverage-text``

    Génère un fichier de log ou une sortie écran sur la ligne de commandes
    dans un format lisible avec les informations de couverture de code pour les tests exécutés.
    Voir :ref:`logging` pour plus de détails.

    Merci de noter que cette fonctionnalité n'est seulement disponible que
    lorsque les extensions tokenizer et Xdebug sont installées.

``--log-junit``

    Génère un fichier de log au format JUnit XML pour les tests exécutés.
    Voir :ref:`logging` pour plus de détails.

``--testdox-html`` et ``--testdox-text``

    Génère la documentation agile au format HTML ou texte pur pour les
    tests exécutés. Voir :ref:`other-uses-for-tests` pour
    plus de détails.

``--filter``

    Exécute seulement les tests dont le nom correspond à l'expression régulière donnée.
    Si le motif n'est pas entouré de délimiteurs, PHPUnit
    inclura le motif dans les délimiteurs ``/``.

    Les noms de test à faire correspondre seront dans l'un des formats suivant :

    ``TestNamespace\TestCaseClass::testMethod``

        Le format de nom de test par défaut est l'équivalent de l'utilisation de
        la constante magique ``__METHOD__`` dans
        la méthode de test.

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        Lorsqu'un test a un fournisseur de données, chaque itération des
        données a l'index courant ajouté à la fin
        du nom de test par défaut.

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        Lorsqu'un test a un fournisseur de données qui utilise des ensembles nommés, chaque
        itération des données a le nom courant ajouté à la
        du nom de test par défaut. Voir
        :numref:`textui.examples.TestCaseClass.php` pour un
        exemple de fournisseurs de données avec des ensembles nommés.

        .. code-block:: php
            :caption: Ensembles de données nommés
            :name: textui.examples.TestCaseClass.php

            <?php
            use PHPUnit\Framework\TestCase;

            namespace TestNamespace;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }
            ?>

    ``/path/to/my/test.phpt``

        Le nom du test pour un test PHPT est le chemin du système de fichiers.

    Voir :numref:`textui.examples.filter-patterns` pour des exemples
    de motifs de filtre valide.

    .. code-block:: php
        :caption: Exemples de motif de filtre
        :name: textui.examples.filter-patterns

    Voir :numref:`textui.examples.filter-shortcuts` pour quelques
    raccourcis supplémentaires disponibles pour faire correspondre
    des fournisseurs de données.

    .. code-block:: php
        :caption: Raccourcis de filtre
        :name: textui.examples.filter-shortcuts

``--testsuite``

    Exécute uniquement la suite de test dont le nom correspond au modèle donné.

``--group``

    Exécute seulement les tests appartenant à un/des groupe(s) indiqué(s). Un test
    peut être signalé comme appartenant à un groupe en utilisant l'annotation ``@group``.

    L'annotation ``@author`` est un alias pour
    ``@group`` permettant de filtrer les tests en se basant
    sur leurs auteurs.

``--exclude-group``

    Exclut les tests d'un/des groupe(s) indiqué(s). Un test peut être signalé
    comme appartenant à un groupe en utilisant l'annotation ``@group``.

``--list-groups``

    Liste les groupes de tests disponibles.

``--test-suffix``

    Recherche seulement les fichiers de test avec le(s) suffixe(s) spécifié(s).

``--dont-report-useless-tests``

    Ne pas signaler les tests qui ne testent rien. Voir :ref:`risky-tests` pour plus de détails.

``--strict-coverage``

    Être strict sur le code non-intentionnellemnt couvert. Voir :ref:`risky-tests` pour plus de détails.

``--strict-global-state``

    Être strict sur la manipulation de l'état global. Voir :ref:`risky-tests` pour plus de détails.

``--disallow-test-output``

    Être strict sur les sorties écran pendant les tests. Voir :ref:`risky-tests` pour plus de détails.

``--disallow-todo-tests``

    Ne pas exécuter les tests qui ont l'annotation ``@todo`` dans son docblock.

``--enforce-time-limit``

    Appliquer une limite de temps basée sur la taille du test. Voir :ref:`risky-tests` pour plus de détails.

``--process-isolation``

    Exécute chaque test dans un processus PHP distinct.

``--no-globals-backup``

    Ne pas sauvegarder et restaurer $GLOBALS. Voir :ref:`fixtures.global-state`
    pour plus de détails.

``--static-backup``

    Sauvegarder et restaurer les attributs statiques des classes définies par l'utilisateur.
    Voir :ref:`fixtures.global-state` pour plus de détails.

``--colors``

    Utiliser des couleurs pour la sortie écran.
    Sur Windows, utiliser `ANSICON <https://github.com/adoxa/ansicon>`_ ou `ConEmu <https://github.com/Maximus5/ConEmu>`_.

    Il existe trois valeurs possible pour cette option:

    -

      ``never`` : Ne jamais afficher de couleurs dans la sortie écran. Il s'agit de la valeur par défaut lorsque l'option ``--colors`` n'est pas utilisée.

    -

      ``auto`` : Afficher les couleurs dans la sortie à moins que le terminal actuel ne supporte pas les couleurs,
      ou si la sortie est envoyée vers une commande ou redirigée vers un fichier.

    -

      ``always`` : Toujours affiche les couleurs dans la sortie écran, même lorsque le terminal en cours ne prend pas en charge les couleurs,
      ou lorsque la sortie est envoyée vers une commande ou redirigée vers un fichier.

    Lorsque ``--colors`` est utilisée sans aucune valeur, ``auto`` est la valeur choisie.

``--columns``

    Définit le nombre de colonnes à utiliser pour la barre de progression.
    Si la valeur définie est ``max``, le nombre de colonnes sera le maximum du terminal courant.

``--stderr``

    Utilise optionnellement ``STDERR`` au lieu de
    ``STDOUT`` pour l'affichage.

``--stop-on-error``

    Arrête l'exécution à la première erreur.

``--stop-on-failure``

    Arrête l'exécution à la première erreur ou au premier échec.

``--stop-on-risky``

    Arrête l'exécution au premier test risqué.

``--stop-on-skipped``

    Arrête l'exécution au premier test sauté.

``--stop-on-incomplete``

    Arrête l'exécution au premier test incomplet.

``--verbose``

    Affiche des informations plus détaillées, par exemple le nom des tests
    qui sont incomplets ou qui ont été sautés.

``--debug``

    Affiche des informations de débogage telles que le nom d'un test quand
    son exécution démarre.

``--loader``

    Indique l'implémentation de ``PHPUnit_Runner_TestSuiteLoader``
    à utiliser.

    Le chargeur standard de suite de tests va chercher les fichiers source
    dans le répertoire de travail actuel et dans chaque répertoire qui
    est indiqué dans la directive de configuration PHP ``include_path``.
    Le nom d'une classe tel que ``Projet_Paquetage_Classe`` est
    calqué sur le nom de fichier source
    :file:`Projet/Paquetage/Classe.php`.

``--repeat``

    Répéter l'exécution du(des) test(s) le nombre indiqué de fois.

``--testdox``

    Rapporte l'avancement des tests sous forme de documentation agile. Voir
    :ref:`other-uses-for-tests` pour plus de détails.

``--printer``

    Indique l'afficheur de résultats à utiliser. Cette classe d'afficheur doit
    hériter de ``PHPUnit_Util_Printer`` et implémenter l'interface
    ``PHPUnit\Framework\TestListener``.

``--bootstrap``

    Un fichier PHP "amorce" ("bootstrap") est exécuté avant les tests.

``--configuration``, ``-c``

    Lit la configuration dans un fichier XML.
    Voir :ref:`appendixes.configuration` pour plus de détails.

    Si :file:`phpunit.xml` ou
    :file:`phpunit.xml.dist` (dans cet ordre) existent dans le
    répertoire de travail actuel et que ``--configuration`` n'est
    *pas* utilisé, la configuration sera automatiquement
    lue dans ce fichier.

    Si un répertoire est spécifié et si
    :file:`phpunit.xml` ou :file:`phpunit.xml.dist` (in that order)
    existe dans ce répertoire, la configuration sera
    automatiquement lue dans ce fichier.

``--no-configuration``

    Ignore :file:`phpunit.xml` et
    :file:`phpunit.xml.dist` du répertoire de travail
    actuel.

``--include-path``

    Préfixe l'``include_path`` PHP avec le(s) chemin(s) donné(s).

``-d``

    Fixe la valeur des options de configuration PHP données.

.. admonition:: Note

   Notez qu'à partir de 4.8, les options peuvent être placées après le(s) argument(s).


