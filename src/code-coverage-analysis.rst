

.. _code-coverage-analysis:

=============================
Analyse de couverture de code
=============================

    *Wikipedia*:

    En informatique, la couverture de code est une mesure utilisée pour décrire le
    taux de code source testé d'un programme lorsqu'il est testé par une suite de
    test particulière. Un programme avec un taux de couverture de code élevée a été plus complètement
    testé et a une plus faible chance de contenir des bugs logiciel qu'un programme
    avec un taux de couverture de code faible.

Dans ce chapitre, vous apprendrez tout sur la fonctionnalité de couverture
de code de PHPUnit qui fournit une vision interne des parties du code de
production qui sont exécutées quand les tests sont exécutés. Elle utilise le composant
`php-code-coverage <https://github.com/sebastianbergmann/php-code-coverage>`_
qui tire parti de la fonctionnalité de couverture de code fournie par l'extension
`Xdebug <http://xdebug.org/>`_ de PHP.

.. admonition:: Note

   Xdebug n'est pas distribué au sein de PHPUnit. Si une notice indiquant que
   l'extension Xdebug n'est pas chargé en lançant les tests, cela signifie que
   Xdebug n'est pas installé ou n'est pas configuré correctement. Avant de pouvoir
   utiliser les fonctionnalités de couverture de code dans PHPUnit, vous devez lire
   `le guide d'installation de Xdebug. <http://xdebug.org/docs/install>`_.

PHPUnit peut générer un rapport de couverture de code HTML aussi bien que
des fichiers de log en XML avec les informations de couverture de code dans différents formats
(Clover, Crap4J, PHPUnit). Les informations de couverture de code peuvent aussi être rapportées
en text (et affichées vers STDOUT) et exportées en PHP pour un traitement
ultérieur.

Reportez-vous à :ref:`textui` pour obtenir la liste des options de ligne de commande
qui contrôlent la fonctionnalité de couverture de code ainsi que :ref:`appendixes.configuration.logging` pour les paramètres de
configuration appropriés.

.. _code-coverage-analysis.metrics:

Indicateurs logiciels pour la couverture de code
################################################

Différents indicateurs logiciels existent pour mesurer la couverture de code :

*Couverture de ligne*

    L'indicateur logiciel de *couverture de ligne* mesure
    si chaque ligne exécutable a été exécutée.

*Couverture de fonction et de méthode*

    L'indicateur logiciel de *couverture de fonction et de méthode*
    mesure si chaque fonction ou méthode a été invoquée.
    php-code-coverage considère qu'une fonction ou une méthode a été couverte
    seulement quand toutes ses lignes exécutables sont couvertes.

*Couverture de classe et de trait*

    L'indicateur logiciel de *couverture de classe et de trait*
    mesure si chaque méthode d'une classe ou d'un trait est couverte.
    php-code-coverage considère qu'une classe ou un trait est couvert
    seulement quand toutes ses méthodes sont couvertes.

*Couverture d'opcode*

    L'indicateur logiciel de *couverture d'opcode* mesure
    si chaque opcode d'une fonction ou d'une méthode a été exécuté lors de l'exécution
    de la suite de test. Une ligne de code se compile habituellement en plus
    d'un opcode. La couverture de ligne considère une ligne de code comme couverte
    dès que l'un de ses opcode est exécuté.

*Couverture de branche*

    L'indicateur logiciel de *couverture de branche* mesure
    si l'expression booléenne de chaque structure de contrôle a été évaluée
    à ``true`` et à ``false`` pendant l'exécution
    de la suite de test.

*Couverture de chemin*

    L'indicateur logiciel de *couverture de chemin* mesure
    si chacun des chemins d'exécution possible dans une fonction ou une méthode
    ont été suivis lors de l'exécution de la suite de test. Un chemin d'exécution est
    une séquence unique de branches depuis l'entrée de la fonction ou
    de la méthode jusqu'à sa sortie.

*L'index Change Risk Anti-Patterns (CRAP)*

    L'index *Change Risk Anti-Patterns (CRAP)* est
    calculé en se basant sur la complexité cyclomatique et la couverture de code
    d'une portion de code. Du code qui n'est pas trop complexe et qui a une couverture
    de code adéquate aura un index CRAP faible. L'index CRAP peut être baissé
    en écrivant des tests et en refactorisant le code pour diminuer sa
    complexité.

.. admonition:: Note

   Les indicateurs logiciel de *Couverture d'Opcode*,
   *Couverture de branche* et de
   *Couverture de chemin* ne sont pas encore
   supportés par php-code-coverage.

.. _code-coverage-analysis.whitelisting-files:

Liste blanche de fichiers
#########################

Il est requis de configurer une *liste blanche* pour dire à
PHPUnit quels fichiers de code source inclure dans le rapport de couverture de code.
Cela peut être fait en utilisant l'option de ligne de commande ``--whitelist``
ou via le fichier de configuration (voir :ref:`appendixes.configuration.whitelisting-files`).

Optionnellement, tous les fichiers en liste blanche peuvent être ajoutés au rapport
de couverture de code en paramétrant ``addUncoveredFilesFromWhitelist="true"``
dans votre fichier de configuration PHPUnit (voir :ref:`appendixes.configuration.whitelisting-files`). Cela autorise
l'inclusion de fichiers qui ne sont pas encore testés du tout. Si vous voulez avoir des
information sur quelles lignes d'un fichier non couvert sont exécutables,
par exemple, vous devez également définir
``processUncoveredFilesFromWhitelist="true"`` dans votre
fichier de configuration PHPUnit (voir :ref:`appendixes.configuration.whitelisting-files`).

.. admonition:: Note

   Notez que le chargement des fichiers de code source qui est effectué lorsque
   ``processUncoveredFilesFromWhitelist="true"`` est défini peut
   causer des problèmes quand un fichier de code source contient du code en dehors de la portée
   d'une classe ou d'une fonction, par exemple.

.. _code-coverage-analysis.ignoring-code-blocks:

Ignorer des blocs de code
#########################

Parfois, vous avez des blocs de code que vous ne pouvez pas tester et que vous pouvez vouloir
ignorer lors de l'analyse de la couverture de code. PHPUnit vous permet de le faire
en utilisant les annotations ``@codeCoverageIgnore``,
``@codeCoverageIgnoreStart`` et
``@codeCoverageIgnoreEnd`` comme montré dans
:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`.

.. code-block:: php
    :caption: Utiliser les annotations ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` et ``@codeCoverageIgnoreEnd``
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore
    ?>

Les lignes de code ignorées (marquées comme ignorées à l'aide des annotations)
sont comptées comme exécutées (si elles sont exécutables) et ne seront pas
mises en évidence.

.. _code-coverage-analysis.specifying-covered-methods:

Spécifier les méthodes couvertes
################################

L'annotation ``@covers`` (voir
:ref:`appendixes.annotations.covers.tables.annotations`) peut être
utilisée dans le code de test pour indiquer quelle(s) méthode(s) une méthode de test
veut tester. Si elle est fournie, seules les informations de couverture de code pour
la(les) méthode(s) indiquées seront prises en considération.
:numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`
montre un exemple.

.. code-block:: php
    :caption: Tests qui indiquent quelle(s) méthode(s) ils veulent couvrir
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }
    ?>

Il est également possible d'indiquer qu'un test ne doit couvrir
*aucune* méthode en utilisant l'annotation
``@coversNothing`` (voir
:ref:`appendixes.annotations.coversNothing`). Ceci peut être
utile quand on écrit des tests d'intégration pour s'assurer que vous
ne générez une couverture de code avec des tests unitaires.

.. code-block:: php
    :caption: Un test qui indique qu'aucune méthode ne doit être couverte
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\DbUnit\TestCase

    class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _code-coverage-analysis.edge-cases:

Cas limites
###########

Cette section présente des cas limites remarquables qui conduisent à des informations
de couverture de code prêtant à confusion.

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // Because it is "line based" and not statement base coverage
    // one line will always have one coverage status
    if (false) this_function_call_shows_up_as_covered();

    // Due to how code coverage works internally these two lines are special.
    // This line will show up as non executable
    if (false)
        // This line will show up as covered because it is actually the
        // coverage of the if statement in the line above that gets shown here!
        will_also_show_up_as_covered();

    // To avoid this it is necessary that braces are used
    if (false) {
        this_call_will_never_show_up_as_covered();
    }
    ?>
