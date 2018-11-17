

.. _extending-phpunit:

===============
Etendre PHPUnit
===============

PHPUnit peut être étendu de multiples façon pour rendre l'écriture des
tests plus simple et personnaliser le retour que vous obtenez des tests
exécutés. Voici les points de départs communs pour étendre PHPUnit.

.. _extending-phpunit.PHPUnit_Framework_TestCase:

Sous-classe PHPUnit\\Framework\\TestCase
########################################

Ecrivez des assertions personnalisées et des méthodes utilitaires dans une sous classe abstraite de
``PHPUnit\Framework\TestCase`` et faites hériter vos classes
de cas de test de cette classe. C'est une des façons les plus faciles pour
étendre PHPUnit.

.. _extending-phpunit.custom-assertions:

Ecrire des assertions personnalisées
####################################

Lorsqu'on écrit des assertions personnalisées, c'est une bonne pratique
de suivre la façon dont PHPUnit implémente ses propres assertions. Comme vous pouvez le voir dans
:numref:`extending-phpunit.examples.Assert.php`, la méthode
``assertTrue()`` ne fait qu'encapsuler les méthodes
``isTrue()`` et ``assertThat()``:
``isTrue()`` crée un objet matcher qui est passé à
``assertThat()`` pour évaluation.

.. code-block:: php
    :caption: Les méthodes assertTrue() et isTrue() de la classe PHPUnit\\Framework\\Assert
    :name: extending-phpunit.examples.Assert.php

    <?php
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\TestCase;

    abstract class Assert
    {
        // ...

        /**
         * Asserts that a condition is true.
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit\Framework\AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Returns a PHPUnit\Framework\Constraint\IsTrue matcher object.
         *
         * @return PHPUnit\Framework\Constraint\IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit\Framework\Constraint\IsTrue;
        }

        // ...
    }

:numref:`extending-phpunit.examples.IsTrue.php` montre comment
``PHPUnit\Framework\Constraint\IsTrue`` étend la classe
abstraite de base pour des objets matcher (ou des contraintes),
``PHPUnit\Framework\Constraint``.

.. code-block:: php
    :caption: La classe PHPUnit\\Framework\\Constraint\\IsTrue
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    class IsTrue extends Constraint
    {
        /**
         * Evaluates the constraint for parameter $other. Returns true if the
         * constraint is met, false otherwise.
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * Returns a string representation of the constraint.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }

L'effort d'implémentation des méthodes ``assertTrue()`` et
``isTrue()`` ainsi que la classe
``PHPUnit\Framework\Constraint\IsTrue`` tire bénéfice du fait que
``assertThat()`` prend automatiquement soin d'évaluer l'assertion et
les tâches de suivi comme le décompte à des fins de statistique.
Plus encore, la méthode ``isTrue()`` peut être utilisée comme un matcher
lors de la configuration d'objets mock.

.. _extending-phpunit.PHPUnit_Framework_TestListener:

Implémenter PHPUnit\\Framework\\TestListener
############################################

:numref:`extending-phpunit.examples.SimpleTestListener.php`
montre une implémentation simple de l'interface
``PHPUnit\Framework\TestListener``.

.. code-block:: php
    :caption: Un simple moniteur de test
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit\Framework\Test $test, \Throwable $e, float $time): void
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addWarning(PHPUnit\Framework\Test $test, PHPUnit\Framework\Warning $e, float $time): void
        {
            printf("Warning while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit\Framework\Test $test, PHPUnit\Framework\AssertionFailedError $e, float $time): void
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit\Framework\Test $test, \Throwable $e, float $time): void
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit\Framework\Test $test, \Throwable $e, float $time): void
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit\Framework\Test $test, \Throwable $e, float $time): void
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit\Framework\Test $test): void
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit\Framework\Test $test, float $time): void
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit\Framework\TestSuite $suite): void
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit\Framework\TestSuite $suite): void
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }


:numref:`extending-phpunit.examples.ExtendedTestListener.php`
montre comment utiliser le trait
``PHPUnit\Framework\TestListenerDefaultImplementation``, qui permet de spécifier uniquement les méthodes d'interface
qui sont intéressantes pour votre cas d'utilisation, tout en fournissant des implémentations vides pour
tous les autres.

.. code-block:: php
    :caption: Utiliser le trait TestListenerDefaultImplementation
    :name: extending-phpunit.examples.ExtendedTestListener.php

    <?php
    use PHPUnit\Framework\TestListener;
    use PHPUnit\Framework\TestListenerDefaultImplementation;

    class ShortTestListener implements TestListener
    {
        use TestListenerDefaultImplementation;

        public function endTest(PHPUnit\Framework\Test $test, float $time): void
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }


Dans :ref:`appendixes.configuration.test-listeners` vous pouvez voir
comment configurer PHPUnit pour brancher votre moniteur de test lors de l'exécution
des tests.

.. _extending-phpunit.PHPUnit_Framework_Test:

Implémenter PHPUnit\\Framework\\Test
####################################

L'interface ``PHPUnit\Framework\Test`` est restreinte et
facile à implémenter. Vous pouvez écrire une implémentation de
``PHPUnit\Framework\Test`` qui est plus simple que
``PHPUnit\Framework\TestCase`` et qui exécute
*des tests dirigés par les données*, par exemple.

:numref:`extending-phpunit.examples.DataDrivenTest.php`
montre une classe de cas de test dirigé par les tests qui compare les valeurs d'un fichier contenant
des valeurs séparées par des virgules (CSV). Chaque ligne d'un tel fichier ressemble à
``foo;bar``, où la première valeur est celle que nous attendons
et la seconde valeur celle constatée.

.. code-block:: php
    :caption: Un test dirigé par les données
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit\Framework\Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit\Framework\TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit\Framework\TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit\Framework\Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit_Framework_AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit\TextUI\TestRunner::run($test);


.. code-block:: bash

    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.
