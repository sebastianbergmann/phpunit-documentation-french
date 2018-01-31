

.. _writing-tests-for-phpunit:

=============================
Écrire des tests pour PHPUnit
=============================

:numref:`writing-tests-for-phpunit.examples.StackTest.php` montre
comment nous pouvons écrire des tests en utilisant PHPUnit pour contrôler
les opérations PHP sur les tableaux. L'exemple introduit les conventions
et les étapes de base pour écrire des tests avec PHPUnit:

#.

   Les tests pour une classe ``Class`` vont dans une classe ``ClassTest``.

#.

   ``ClassTest`` hérite (la plupart du temps) de ``PHPUnit\Framework\TestCase``.

#.

   Les tests sont des méthodes publiques qui sont appelées ``test*``.

   Alternativement, vous pouvez utiliser l'annotation ``@test`` dans le bloc de documentation d'une méthode pour la marquer comme étant une méthode de test.

#.

   A l'intérieur des méthodes de test, des méthodes d'assertion telles que ``assertEquals()`` (voir :ref:`appendixes.assertions`) sont utilisées pour affirmer qu'une valeur constatée correspond à une valeur attendue.

.. code-block:: php
    :caption: Tester des opérations de tableau avec PHPUnit
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testPushAndPop()
        {
            $stack = [];
            $this->assertEquals(0, count($stack));

            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertEquals(1, count($stack));

            $this->assertEquals('foo', array_pop($stack));
            $this->assertEquals(0, count($stack));
        }
    }
    ?>
|
    *Martin Fowler*:

    A chaque fois que vous avez la tentation de saisir quelque chose dans une
    instruction ``print`` ou dans une expression de débogage,
    écrivez le plutôt dans un test.

.. _writing-tests-for-phpunit.test-dependencies:

Dépendances des tests
#####################

    *Adrian Kuhn et. al.*:

    Les tests unitaires sont avant tout écrits comme étant une bonne pratique destinée à aider les développeurs
    à identifier et corriger les bugs, à refactoriser le code et à servir de documentation
    pour une unité du logiciel testé. Pour obtenir ces avantages, les tests unitaires
    doivent idéalement couvrir tous les chemins possibles du programme. Un test unitaire
    couvre usuellement un unique chemin particulier d'une seule fonction ou méthode. Cependant, une
    méthode de test n'est pas obligatoirement une entité encapsulée et indépendante. Souvent,
    il existe des dépendances implicites entre les méthodes de test, cachées
    dans l'implémentation du scénario d'un test.

PHPUnit gère la déclaration de dépendances explicites entre les
méthodes de test. De telles dépendances ne définissent pas l'ordre dans lequel les méthodes de test
doivent être exécutées, mais elles permettent de renvoyer une instance de
la fixture de test par un producteur à des consommateurs qui en dépendent.

-

  Un producteur est une méthode de test qui produit ses éléments testées comme valeur de retour.

-

  Un consommateur est une méthode de test qui dépend d'un ou plusieurs producteurs et de leurs valeurs de retour.

:numref:`writing-tests-for-phpunit.examples.StackTest2.php` montre comment
utiliser l'annotation ``@depends`` pour exprimer des dépendances
entre des méthodes de test.

.. code-block:: php
    :caption: Utiliser l'annotation ``@depends`` pour exprimer des dépendances
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testEmpty()
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack)
        {
            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack)
        {
            $this->assertEquals('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }
    ?>

Dans l'exemple ci-dessus, le premier test, ``testEmpty()``,
crée un nouveau tableau et affirme qu'il est vide. Le test renvoie ensuite
la fixture comme résultat. Le deuxième test, ``testPush()``,
dépend de ``testEmpty()`` et reçoit le résultat de ce test
dont il dépend comme argument. Enfin, ``testPop()``
dépend de ``testPush()``.

.. admonition:: Note

   La valeur de retour produite par un producteur est passée "telle quelle" à son
   consommateur par défaut. Cela signifie que lorsqu'un producteur renvoie un objet,
   une référence vers cet objet est passée a son consommateur. Lorsqu'une copie
   doit être utilisée au lieu d'une référence, alors @depends clone
   doit être utilisé au lieu de @depends.

Pour localiser rapidement les défauts, nous voulons que notre attention soit
retenue par les tests en échecs pertinents. C'est pourquoi PHPUnit saute
l'exécution d'un test quand un test dont il dépend a échoué. Ceci améliore la
localisation des défauts en exploitant les dépendances entre les tests comme montré dans
:numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`.

.. code-block:: php
    :caption: Exploiter les dépendances entre les tests
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyFailureTest extends TestCase
    {
        public function testOne()
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyFailureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

Un test peut avoir plusieurs annotations ``@depends``.
PHPUnit ne change pas l'ordre dans lequel les tests sont exécutés, vous
devez donc vous assurer que les dépendances d'un test peuvent effectivement
être utilisables avant que le test ne soit lancé.

Un test qui a plusieurs annotations ``@depends``
prendra une fixture du premier producteur en premier argument, une fixture
du second producteur en second argument, et ainsi de suite.
Voir :numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`

.. code-block:: php
    :caption: Test avec plusieurs dépendances
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php
    use PHPUnit\Framework\TestCase;

    class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 3 assertions)

.. _writing-tests-for-phpunit.data-providers:

Fournisseur de données
######################

Une méthode de test peut recevoir des arguments arbitraires. Ces arguments doivent
être fournis par une méthode fournisseuse de données (``additionProvider()`` dans
:numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
La méthode fournisseuse de données à utiliser est indiquée dans l'annotation
``@dataProvider``.

Une méthode fournisseuse de données doit être ``public`` et retourne, soit
un tableau de tableaux, soit un objet qui implémente l'interface ``Iterator``
et renvoie un tableau pour chaque itération. Pour chaque tableau qui est une partie de
l'ensemble, la méthode de test sera appelée avec comme arguments le contenu
du tableau.

.. code-block:: php
    :caption: Utiliser un fournisseur de données qui renvoie un tableau de tableaux
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

Lorsque vous utilisez un grand nombre de jeux de données, il est utile de nommer chacun avec une clé en chaine de caractère au lieu de la valeur numérique par défaut.
La sortie sera plus verbeuse car elle contiendra le nom du jeu de données qui casse un test.

.. code-block:: php
    :caption: Utiliser un fournisseur de données avec des jeux de données nommés
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Utiliser un fournisseur de données qui renvoie un objet Iterator
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    require 'CsvFileIterator.php';

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return new CsvFileIterator('data.csv');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 matches expected '3'.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: La classe CsvFileIterator
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php
    use PHPUnit\Framework\TestCase;

    class CsvFileIterator implements Iterator {
        protected $file;
        protected $key = 0;
        protected $current;

        public function __construct($file) {
            $this->file = fopen($file, 'r');
        }

        public function __destruct() {
            fclose($this->file);
        }

        public function rewind() {
            rewind($this->file);
            $this->current = fgetcsv($this->file);
            $this->key = 0;
        }

        public function valid() {
            return !feof($this->file);
        }

        public function key() {
            return $this->key;
        }

        public function current() {
            return $this->current;
        }

        public function next() {
            $this->current = fgetcsv($this->file);
            $this->key++;
        }
    }
    ?>

Quand un test reçoit des entrées à la fois d'une méthode ``@dataProvider``
et d'un ou plusieurs tests dont il ``@depends``,
les arguments provenant du fournisseur de données arriveront avant
ceux des tests dont il dépend. Les arguments des tests dépendants seront les
mêmes pour chaque jeux de données
Voir :numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`

.. code-block:: php
    :caption: Combinaison de @depends et @dataProvider dans le même test
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider()
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
    Array (
    -    0 => 'provider1'
    +    0 => 'provider2'
    1 => 'first'
    2 => 'second'
    )

    /home/sb/DependencyAndDataProviderComboTest.php:31

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. admonition:: Note

   Quand un test dépend d'un test qui utilise des fournisseurs de données,
   le test dépendant sera exécuté quand le test dont il dépend réussira pour
   au moins un jeu de données. Le résultat d'un test qui utilise des fournisseurs
   de données ne peut pas être injecté dans un test dépendant.

.. admonition:: Note

   Tous les fournisseurs de données sont exécutés avant le premier appel à la méthode statique ``setUpBeforeClass``
   et le premier appel à la méthode ``setUp``.
   De ce fait, vous ne pouvez accéder à aucune variable créée à ces endroits
   depuis un fournisseur de données. Ceci est requis pour que PHPUnit puisse
   calculer le nombre total de tests.

.. _writing-tests-for-phpunit.exceptions:

Tester des exceptions
#####################

:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
montre comment utiliser la méthode ``expectException()`` pour tester
si une exception est levée par le code testé.

.. code-block:: php
    :caption: Utiliser la méthode expectException()
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        public function testException()
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

En complément à la méthode ``expectException()`` les méthodes
``expectExceptionCode()``,
``expectExceptionMessage()`` et
``expectExceptionMessageRegExp()`` existent pour établir
des attentes pour les exceptions levées par le code testé.

.. admonition:: Note

   Notez que expectExceptionMessage affirme que message ``$actual``
   contient le message ``$expected`` et n'effectue pas
   une comparaison exacte de chaîne.

Alternativement, vous pouvez utiliser les annotations ``@expectedException``,
``@expectedExceptionCode``,
``@expectedExceptionMessage`` et
``@expectedExceptionMessageRegExp`` pour établir
des attentes pour les exceptions levées par le code testé.
:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php`
montre un exemple.

.. code-block:: php
    :caption: Utiliser l'annotation @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        /**
         * @expectedException InvalidArgumentException
         */
        public function testException()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.errors:

Tester les erreurs PHP
######################

Par défaut, PHPUnit convertit les erreurs, avertissements et remarques PHP
qui sont émises lors de l'exécution d'un test en exception. En utilisant ces
exceptions, vous pouvez, par exemple, attendre d'un test qu'il produise une erreur
PHP comme montré dans :numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`.

.. admonition:: Note

   La configuration d'exécution PHP ``error_reporting`` peut
   limiter les erreurs que PHPUnit convertira en exceptions. Si vous rencontrez
   des problèmes avec cette fonctionnalité, assurez-vous que PHP n'est pas configuré
   pour supprimer le type d'erreurs que vous testez.

.. code-block:: php
    :caption: Attendre une erreur PHP en utilisant @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExpectedErrorTest extends TestCase
    {
        /**
          @expectedException PHPUnit\Framework\Error
         */
        public function testFailingInclude()
        {
            include 'not_existing_file.php';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit -d error_reporting=2 ExpectedErrorTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 0 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

``PHPUnit\Framework\Error\Notice`` et
``PHPUnit\Framework\Error\Warning`` représentent respectivement
les remarques et les avertissements PHP.

.. admonition:: Note

   Vous devriez être aussi précis que possible lorsque vous testez des exceptions.
   Tester avec des classes qui sont trop génériques peut conduire à des effets de
   bord indésirables. C'est pourquoi tester la présence de la classe
   ``Exception`` avec ``@expectedException`` ou
   ``setExpectedException()`` n'est plus autorisé.

Quand les tests s'appuient sur des fonctions php qui déclenchent des erreurs
comme ``fopen``, il peut parfois être utile d'utiliser la
suppression d'erreur lors du test. Ceci permet de contrôler les valeurs de retour
en supprimant les remarques qui auraient conduit à une
``PHPUnit\Framework\Error\Notice`` de phpunit.

.. code-block:: php
    :caption: Tester des valeurs de retour d'un code source qui utilise des erreurs PHP
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting() {
            $writer = new FileWriter;
            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }
    class FileWriter
    {
        public function write($file, $content) {
            $file = fopen($file, 'w');
            if($file == false) {
                return false;
            }
            // ...
        }
    }

    ?>

.. code-block:: bash

    $ phpunit ErrorSuppressionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

Sans la suppression d'erreur, le test échouerait à rapporter
``fopen(/is-not-writeable/file): failed to open stream:
    No such file or directory``.

.. _writing-tests-for-phpunit.output:

Tester la sortie écran
######################

Quelquefois, vous voulez confirmer que l'exécution d'une méthode, par
exemple, produit une sortie écran donnée (via ``echo`` ou
``print``, par exemple). La classe
``PHPUnit\Framework\TestCase`` utilise la fonctionnalité de
`mise en tampon de la sortie écran <http://www.php.net/manual/en/ref.outcontrol.php>`_ de PHP pour fournir la fonctionnalité qui est
nécessaire pour cela.

:numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`
montre comment utiliser la méthode ``expectOutputString()`` pour
indiquer la sortie écran attendue. Si la sortie écran attendue n'est pas générée, le test
sera compté comme étant en échec.

.. code-block:: php
    :caption: Tester la sortie écran d'une fonction ou d'une méthode
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo()
        {
            $this->expectOutputString('foo');
            print 'foo';
        }

        public function testExpectBarActualBaz()
        {
            $this->expectOutputString('bar');
            print 'baz';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit OutputTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

:numref:`writing-tests-for-phpunit.output.tables.api`
montre les méthodes fournies pour tester les sorties écran

.. rst-class:: table
.. list-table:: Méthodes pour tester les sorties écran
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - Méthode
      - Signification
    * - ``void expectOutputRegex(string $regularExpression)``
      - Indique que l'on s'attend à ce que la sortie écran corresponde à une expression régulière ``$regularExpression``.
    * - ``void expectOutputString(string $attenduString)``
      - Indique que l'on s'attend que la sortie écran soit égale à une chaine de caractère ``$expectedString``.
    * - ``bool setOutputCallback(callable $callback)``
      - Configure une fonction de rappel (callback) qui est utilisée, par exemple, formater la sortie écran effective.
    * - ``string getActualOutput()``
      - Renvoi la sortie écran courrante.

.. admonition:: Note

   En mode strict, un test qui produit une sortie écran échouera.

.. _writing-tests-for-phpunit.error-output:

Sortie d'erreur
###############

Chaque fois qu'un test échoue, PHPUnit essaie de vous fournir le plus
de contexte possible pour identifier le problème.

.. code-block:: php
    :caption: Sortie d'erreur générée lorsqu'un échec de comparaison de tableau
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Dans cet exemple, une seule des valeurs du tableau diffère et les autres valeurs
sont affichées pour fournir un contexte sur l'endroit où l'erreur s'est produite.

Lorsque la sortie générée serait longue à lire, PHPUnit la divisera
et fournira quelques lignes de contexte autour de chaque différence.

.. code-block:: php
    :caption: Sortie d'erreur quand une comparaison de long tableaux échoue
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class LongArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LongArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

Cas limite
==========

Quand une comparaison échoue, PHPUnit crée une représentation textuelle des
valeurs d'entrées et les compare. A cause de cette implémentation un diff
peut montrer plus de problèmes qu'il n'en existe réellement.

Cela arrive seulement lors de l'utilisation de assetEquals ou d'autres fonction
de comparaison "faible" sur les tableaux ou les objets.

.. code-block:: php
    :caption: Cas limite dans la génération de la différence lors de l'utilisation de comparaison faible
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayWeakComparisonTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Dans cet exemple, la différence dans le premier indice entre
``1`` et ``'1'``
est signalée même si AssertEquals considère les valeurs comme une correspondance.


