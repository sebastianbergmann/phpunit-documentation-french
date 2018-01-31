

.. _appendixes.assertions:

==========
Assertions
==========

Cette annexe liste les différentes méthodes d'assertion disponibles.

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

De l'utilisation Statique vs. Non-Statique des méthodes d'assertion
###################################################################

Les assertions de PHPUnitsont implémentées dans PHPUnit\\Framework\\Assert.
PHPUnit\\Framework\\TestCase hérite de PHPUnit\\Framework\\Assert.

Les méthodes d'assertion sont declarées static et peuvent être appelées
depuis n'importe quel contexte en utilisant PHPUnit\\Framework\\Assert::assertTrue(),
par exemple, ou en utilisant $this->assertTrue() ou self::assertTrue(),
par exemple, dans une classe qui étends PHPUnit\\Framework\\TestCase.

En fait, vous pouvez même utiliser des fonctions globales d'encapsulation comme assertTrue() dans
n'importe quel contexte (y compris les classes qui étendent PHPUnit\\Framework\\TestCase)
quand vous incluez (manuellement) le fichier de code source :file:`src/Framework/Assert/Functions.php`
fournit avec PHPUnit.

Une question fréquente, surtout de la part des développeurs qui ne connaissent pas PHPUnit, est si
utiliser $this->assertTrue() ou self::assertTrue(),
par exemple, est "la bonne façon" d'appeler une assertion. La réponse courte est : il
n'y a pas de bonne façon. Et il n'y a pas de mauvaise façon non plus. C'est une
question de préférence personnelle.

Pour la plupart des gens, il "semble juste" d'utiliser $this->assertTrue()
parce que la méthode de test est appelée sur un objet de test. Le fait que les méthodes
d'assertion soient déclarées static autorise leur (ré)utilisation
en dehors du scope d'un objet de test. Enfin, les fonctions globales
d'encapsulation permettent aux développeurs de taper moins de caractères (assertTrue() au lieu de
$this->assertTrue() ou self::assertTrue()).

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le tableau ``$array`` ne dispose pas de la clé ``$key``.

``assertArrayNotHasKey()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertArrayHasKey()
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayHasKeyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$className::attributeName`` n'existe pas.

``assertClassNotHasAttribute()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertClassHasAttribute()
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ClassHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertArraySubset:

assertArraySubset()
###################

``assertArraySubset(array $subset, array $array[, bool $strict = '', string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$array`` ne contient pas le ``$subset``.

``$strict`` indique de comparer l'identité des objets dans les tableaux.

.. code-block:: php
    :caption: Utilisation de assertArraySubset()
    :name: appendixes.assertions.assertArraySubset.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArraySubsetTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArraySubsetTest
    PHPUnit 6.5.0 by Sebastian Bergmann.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$className::attributeName`` n'existe pas.

``assertClassNotHasStaticAttribute()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertClassHasStaticAttribute()
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$needle`` n'est pas un élément de ``$haystack``.

``assertNotContains()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeContains()`` et ``assertAttributeNotContains()`` sont des encapsulateurs de commodités qui utilisent un attribut ``public``, ``protected``, ou ``private`` d'une classe ou d'un objet en tant que haystack.

.. code-block:: php
    :caption: Utilisation de assertContains()
    :name: appendixes.assertions.assertContains.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = false])``

Signale une erreur identifiée par ``$message`` si ``$needle`` n'est pas une sous-chaine de ``$haystack``.

Si ``$ignoreCase`` est ``true``, ce test sera sensible à la casse.

.. code-block:: php
    :caption: Utilisation de assertContains()
    :name: appendixes.assertions.assertContains.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. code-block:: php
    :caption: Utilisation de assertContains() with $ignoreCase
    :name: appendixes.assertions.assertContains.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = null, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$haystack`` ne contient pas seulement des valeurs du type ``$type``.

``$isNativeType`` indique si ``$type`` est un type PHP natif ou non.

``assertNotContainsOnly()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeContainsOnly()`` et ``assertAttributeNotContainsOnly()`` sont des encapsulateurs de commodités qui utilisent l'attribut ``public``, ``protected``, ou ``private`` d'une classe ou d'un objet en tant que haystack.

.. code-block:: php
    :caption: Utilisation de assertContainsOnly()
    :name: appendixes.assertions.assertContainsOnly.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsOnlyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$haystack`` ne contient pas seulement des intance de la classe ``$classname``.

.. code-block:: php
    :caption: Utilisation de assertContainsOnlyInstancesOf()
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le nombre d'éléments dans ``$haystack`` n'est pas ``$expectedCount``.

``assertNotCount()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertCount()
    :name: appendixes.assertions.assertCount.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CountTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, ['foo']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit CountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le répertoire spécifié par ``$directory`` n'existe pas.

``assertDirectoryNotExists()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertDirectoryExists()
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le répertoire spécifié par ``$directory`` n'est pas un répertoire ou n'est pas accessible en lecture.

``assertDirectoryNotIsReadable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertDirectoryIsReadable()
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le répertoire spécifié par ``$directory`` n'est pas un répertoire accessible en écriture.

``assertDirectoryNotIsWritable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertDirectoryIsWritable()
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$actual`` n'est pas vide.

``assertNotEmpty()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeEmpty()`` et ``assertAttributeNotEmpty()`` sont des encapsulateurs de commodités qui peuvent être appliqués à un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet.

.. code-block:: php
    :caption: Utilisation de assertEmpty()
    :name: appendixes.assertions.assertEmpty.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EmptyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(['foo']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EmptyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEqualXMLStructure:

assertEqualXMLStructure()
#########################

``assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = false, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la structure XML du DOMElement dans ``$actualElement`` n'est pas égale à la structure XML du DOMElement dans ``$expectedElement``.

.. code-block:: php
    :caption: Utilisation de assertEqualXMLStructure()
    :name: appendixes.assertions.assertEqualXMLStructure.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualXMLStructureTest extends TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, true
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualXMLStructureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si les deux variables ``$expected`` et ``$actual`` ne sont pas égales.

``assertNotEquals()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeEquals()`` et ``assertAttributeNotEquals()`` sont des encapsulateurs de commodités qui utilisent un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertEquals()
    :name: appendixes.assertions.assertEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

Des comparaisons plus spécialisées sont utilisés pour des types d'arguments spécifiques pour ``$expected`` et ``$actual``, voir ci-dessous.

``assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])``

Signale une erreur identifiée par ``$message`` si l'écart absolu entre les deux nombres réels ``$expected`` et ``$actual`` est plus grand que ``$delta``. Si la difference absolue entre les deux nombre flotant ``$expected`` et ``$actual`` est inférieur *ou égal  à* ``$delta``, l'assertion passera.

Lisez "`What Every Computer Scientist Should Know About Floating-Point Arithmetic <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_" pour comprendre pourquoi ``$delta`` est nécessaire.

.. code-block:: php
    :caption: Utilisation de assertEquals() avec des nombres réels
    :name: appendixes.assertions.assertEquals.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.2);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la forme canonique sans commentaires des documents XML représentés par les deux objets DOMDocument ``$expected`` et ``$actual`` ne sont pas égaux.

.. code-block:: php
    :caption: Utilisation de assertEquals() avec des objets DOMDocument
    :name: appendixes.assertions.assertEquals.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si les deux objets ``$expected`` et ``$actual`` n'ont pas les attributs avec des valeurs égales.

.. code-block:: php
    :caption: Utilisation de assertEquals() avec des objets
    :name: appendixes.assertions.assertEquals.example4

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si les deux tableaux ``$expected`` et ``$actual`` ne sont pas égaux.

.. code-block:: php
    :caption: Utilisation de assertEquals() avec des tableaux
    :name: appendixes.assertions.assertEquals.example5

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$condition`` est ``true``.

``assertNotFalse()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertFalse()
    :name: appendixes.assertions.assertFalse.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FalseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(true);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FalseTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier spécifié par ``$expected`` n'a pas les mêmes contenus que le fichier spécifié par ``$actual``.

``assertFileNotEquals()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertFileEquals()
    :name: appendixes.assertions.assertFileEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileEqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileEqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier spécifié par ``$filename`` n'existe pas.

``assertFileNotExists()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertFileExists()
    :name: appendixes.assertions.assertFileExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier spécifié par ``$filename`` n'est pas un fichier ou n'est pas accessible en lecture.

``assertFileNotIsReadable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertFileIsReadable()
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier spécifié par ``$filename`` n'est pas un fichier ou n'est pas accessible en écriture.

``assertFileNotIsWritable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertFileIsWritable()
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actual`` n'est pas plus élevée que la valeur de ``$expected``.

``assertAttributeGreaterThan()`` est un encapsulateur de commodité qui utilise un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertGreaterThan()
    :name: appendixes.assertions.assertGreaterThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreaterThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actual`` n'est pas plus grande ou égale à la valeur de ``$expected``.

``assertAttributeGreaterThanOrEqual()`` est un encapsulateur de commodité qui utilise un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertGreaterThanOrEqual()
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$variable`` n'est pas ``INF``.

``assertFinite()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertInfinite()
    :name: appendixes.assertions.assertInfinite.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InfiniteTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InfiniteTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$actual`` n'est pas une instance de ``$expected``.

``assertNotInstanceOf()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeInstanceOf()`` et ``assertAttributeNotInstanceOf()`` sont des encapsulateurs de commodités qui peuvent être appliqués à un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet.

.. code-block:: php
    :caption: Utilisation de assertInstanceOf()
    :name: appendixes.assertions.assertInstanceOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InstanceOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InstanceOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInternalType:

assertInternalType()
####################

``assertInternalType($expected, $actual[, $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$actual`` n'est pas du type ``$expected``.

``assertNotInternalType()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeInternalType()`` et ``assertAttributeNotInternalType()`` sont des encapsulateurs de commodités qui peuvent être appliqués a un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet.

.. code-block:: php
    :caption: Utilisation de assertInternalType()
    :name: appendixes.assertions.assertInternalType.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InternalTypeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InternalTypeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier ou le répertoire spécifié par ``$filename`` n'est pas accessible en lecture.

``assertNotIsReadable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertIsReadable()
    :name: appendixes.assertions.assertIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le fichier ou le répertoire spécifié par ``$filename`` n'est pas accessible en écriture.

``assertNotIsWritable()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertIsWritable()
    :name: appendixes.assertions.assertIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actualFile`` ne correspond pas à la valeur de
``$expectedFile``.

.. code-block:: php
    :caption: Utilisation de assertJsonFileEqualsJsonFile()
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actualJson`` ne correspond pas à la valeur de
``$expectedFile``.

.. code-block:: php
    :caption: Utilisation de assertJsonStringEqualsJsonFile()
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actualJson`` ne correspond pas à la valeur de
``$expectedJson``.

.. code-block:: php
    :caption: Utilisation de assertJsonStringEqualsJsonString()
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actual`` n'est pas plus petit que ``$expected``.

``assertAttributeLessThan()`` est un encapsulateur de commodité qui utilise un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertLessThan()
    :name: appendixes.assertions.assertLessThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si la valeur de ``$actual`` n'est pas plus petite ou égale à la valeur de ``$expected``.

``assertAttributeLessThanOrEqual()`` est un encapsulateur de commodité qui utilise un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertLessThanOrEqual()
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$variable`` n'est pas ``NAN``.

.. code-block:: php
    :caption: Utilisation de assertNan()
    :name: appendixes.assertions.assertNan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$variable`` n'est pas ``null``.

``assertNotNull()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertNull()
    :name: appendixes.assertions.assertNull.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NullTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NotNullTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$object->attributeName`` n'existe pas.

``assertObjectNotHasAttribute()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertObjectHasAttribute()
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ObjectHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertRegExp:

assertRegExp()
##############

``assertRegExp(string $pattern, string $string[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$string`` ne correspond pas a l'expression régulière ``$pattern``.

``assertNotRegExp()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertRegExp()
    :name: appendixes.assertions.assertRegExp.example

    <?php
    use PHPUnit\Framework\TestCase;

    class RegExpTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit RegExpTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si  ``$string`` ne correspond pas à la chaine ``$format``.

``assertStringNotMatchesFormat()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertStringMatchesFormat()
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Le format de chaine peut contenir les arguments suivants:

-

  ``%e``: Représente un séparateur de répertoire, par exemple ``/`` sur Linux.

-

  ``%s``: Un ou plusieurs de n'importe quoi (caractère ou espace) sauf le caractère de fin de ligne.

-

  ``%S``: Zéro ou plusieurs de n'importe quoi (caractère ou espace) sauf le caractère de fin de ligne.

-

  ``%a``: Un ou plusieurs de n'importe quoi (caractère ou espace) incluant le caractère de fin de ligne.

-

  ``%A``: Zéro ou plusieurs de n'importe quoi (caractère ou espace) incluant le caractère de fin de ligne.

-

  ``%w``: Zéro ou plusieurs caractères espace.

-

  ``%i``: Une valeur entière signée, par exemple ``+3142``, ``-3142``.

-

  ``%d``: Une valeur entière non signée, par exemple ``123456``.

-

  ``%x``: Un ou plusieurs caractères hexadécimaux. Ce sont les caractères compris entre ``0-9``, ``a-f``, ``A-F``.

-

  ``%f``: Un nombre à virgule flottante, par exemple: ``3.142``, ``-3.142``, ``3.142E-10``, ``3.142e+10``.

-

  ``%c``: Un caractère unique quel qu'il soit.

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$string`` ne correspond pas au contenu du ``$formatFile``.

``assertStringNotMatchesFormatFile()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertStringMatchesFormatFile()
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si les deux variables ``$expected`` et ``$actual`` ne sont pas du même type et n'ont pas la même valeur.

``assertNotSame()`` est l'inverse de cette assertion et prend les mêmes arguments.

``assertAttributeSame()`` et ``assertAttributeNotSame()`` sont des encapsulateurs de commodités qui utilisent un attribut ``public``, ``protected`` ou ``private`` d'une classe ou d'un objet en tant que valeur.

.. code-block:: php
    :caption: Utilisation de assertSame()
    :name: appendixes.assertions.assertSame.example

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si les deux variables ``$expected`` et ``$actual`` ne référencent pas le même objet.

.. code-block:: php
    :caption: Utilisation de assertSame() with objects
    :name: appendixes.assertions.assertSame.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$string`` ne termine pas par ``$suffix``.

``assertStringEndsNotWith()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertStringEndsWith()
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEndsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEndsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

Signale une erreur identifiée par ``$message`` le fichier spécifié par ``$expectedFile`` n'a pas ``$actualString`` comme contenu.

``assertStringNotEqualsFile()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertStringEqualsFile()
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEqualsFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEqualsFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$string`` ne commence pas par ``$prefix``.

``assertStringStartsNotWith()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertStringStartsWith()
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringStartsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringStartsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

Des assertions plus complexes peuvent être formulées en utilisant les
classes ``PHPUnit_Framework_Constraint``. Elles peuvent être
évaluées avec la méthode ``assertThat()``.
:numref:`appendixes.assertions.assertThat.example` montre comment les contraintes
``logicalNot()`` et ``equalTo()``
peuvent être utilisées pour exprimer la même assertion que
``assertNotEquals()``.

``assertThat(mixed $value, PHPUnit_Framework_Constraint $constraint[, $message = ''])``

Signale une erreur identifiée par ``$message``si ``$value`` ne correspond pas à ``$constraint``.

.. code-block:: php
    :caption: Utilisation de assertThat()
    :name: appendixes.assertions.assertThat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class BiscuitTest extends TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }
    ?>

:numref:`appendixes.assertions.assertThat.tables.constraints` montre les
classes ``PHPUnit_Framework_Constraint`` disponibles.

.. rst-class:: table
.. list-table:: Contraintes
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - Contrainte
      - Signification
    * - ``PHPUnit_Framework_Constraint_Attribute attribute(PHPUnit_Framework_Constraint $constraint, $attributeName)``
      - Contrainte qui applique une autre contrainte à un attribut d'une classe ou d'un objet.
    * - ``PHPUnit_Framework_Constraint_IsAnything anything()``
      - Contrainte qui accepte toute valeur d'entrée.
    * - ``PHPUnit_Framework_Constraint_ArrayHasKey arrayHasKey(mixed $key)``
      - Contrainte qui valide que le tableau évalué a une clé donnée.
    * - ``PHPUnit_Framework_Constraint_TraversableContains contains(mixed $value)``
      - Contrainte qui valide que le ``tableau`` ou l'objet qui implémente l'interface ``Iterator`` évalué contient une valeur donnée.
    * - ``PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnly(string $type)``
      - Contrainte qui valide que le ``tableau`` ou l'objet qui implémente l'interface ``Iterator`` évalué ne contient que des valeurs d'un type donné.
    * - ``PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - Contrainte qui valide que le ``tableau`` ou l'objet qui implémente l'interface ``Iterator`` évalué ne contient que des instances d'une classe donnée.
    * - ``PHPUnit_Framework_Constraint_IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - Contrainte qui vérifie si une valeur est égale à une autre.
    * - ``PHPUnit_Framework_Constraint_Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)``
      - Contrainte qui vérifie si une valeur est égale à un attribut d'une classe ou d'un objet.
    * - ``PHPUnit_Framework_Constraint_DirectoryExists directoryExists()``
      - Contrainte qui vérifie si le répertoire évalué existe.
    * - ``PHPUnit_Framework_Constraint_FileExists fileExists()``
      - Contrainte qui vérifie si le fichier (name) évalué existe.
    * - ``PHPUnit_Framework_Constraint_IsReadable isReadable()``
      - Contrainte qui vérifie si le fichier (name) évalué est accessible en écriture.
    * - ``PHPUnit_Framework_Constraint_IsWritable isWritable()``
      - Contrainte qui vérifie si le fichier (name) évalué est accessible en écriture.
    * - ``PHPUnit_Framework_Constraint_GreaterThan greaterThan(mixed $value)``
      - Contrainte qui valide que la valeur évaluée est supérieure à une valeur donnée.
    * - ``PHPUnit_Framework_Constraint_Or greaterThanOrEqual(mixed $value)``
      - Contrainte qui valide que la valeur évaluée est supérieure ou égale à une valeur donnée.
    * - ``PHPUnit_Framework_Constraint_ClassHasAttribute classHasAttribute(string $attributeName)``
      - La contrainte qui valide que la classe évaluée possède un attribut donné.
    * - ``PHPUnit_Framework_Constraint_ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - La contrainte qui valide que la classe évaluée possède un attribut statique donné.
    * - ``PHPUnit_Framework_Constraint_ObjectHasAttribute hasAttribute(string $attributeName)``
      - La contrainte qui valide que l'objet évalué possède un attribut donné.
    * - ``PHPUnit_Framework_Constraint_IsIdentical identicalTo(mixed $value)``
      - Contrainte qui valide qu'une valeur est identique à une autre.
    * - ``PHPUnit_Framework_Constraint_IsFalse isFalse()``
      - Contrainte qui valide qu'une valeur évaluée est ``false``.
    * - ``PHPUnit_Framework_Constraint_IsInstanceOf isInstanceOf(string $className)``
      - La contrainte qui VALIDE que l'objet évalué est une instance d'une classe donnée.
    * - ``PHPUnit_Framework_Constraint_IsNull isNull()``
      - Contrainte qui valide qu'une valeur évaluée est ``null``.
    * - ``PHPUnit_Framework_Constraint_IsTrue isTrue()``
      - Contrainte qui valide qu'une valeur évaluée est ``true``.
    * - ``PHPUnit_Framework_Constraint_IsType isType(string $type)``
      - Contrainte qui valide que la valeur évaluée est d'un type spécifié.
    * - ``PHPUnit_Framework_Constraint_LessThan lessThan(mixed $value)``
      - Constraint that asserts that the value it is evaluated for is smaller than a given value.
    * - ``PHPUnit_Framework_Constraint_Or lessThanOrEqual(mixed $value)``
      - Contrainte qui valide que la valeur évaluée est inférieure ou égale à une valeur donnée.
    * - ``logicalAnd()``
      - ET logique (AND).
    * - ``logicalNot(PHPUnit_Framework_Constraint $constraint)``
      - NON logique (NOT).
    * - ``logicalOr()``
      - OU logique (OR).
    * - ``logicalXor()``
      - OU exclusif logique (XOR).
    * - ``PHPUnit_Framework_Constraint_PCREMatch matchesRegularExpression(string $pattern)``
      - Contrainte qui valide que la chaine évaluée correspond à une expression régulière.
    * - ``PHPUnit_Framework_Constraint_StringContains stringContains(string $string, bool $case)``
      - Contrainte qui valide que la chaine évaluée contient une chaine donnée.
    * - ``PHPUnit_Framework_Constraint_StringEndsWith stringEndsWith(string $suffix)``
      - Contrainte qui valide que la chaine évaluée termine par un suffixe donné.
    * - ``PHPUnit_Framework_Constraint_StringStartsWith stringStartsWith(string $prefix)``
      - Contrainte qui valide que la chaine évaluée commence par un préfixe donné.

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si ``$condition`` est ``false``.

``assertNotTrue()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertTrue()
    :name: appendixes.assertions.assertTrue.example

    <?php
    use PHPUnit\Framework\TestCase;

    class TrueTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(false);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit TrueTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le document XML dans ``$actualFile`` n'est pas égal au document XML dans ``$expectedFile``.

``assertXmlFileNotEqualsXmlFile()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertXmlFileEqualsXmlFile()
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le document XML dans ``$actualXml`` n'est pas égal au document XML dans ``$expectedFile``.

``assertXmlStringNotEqualsXmlFile()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertXmlStringEqualsXmlFile()
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

Signale une erreur identifiée par ``$message`` si le document XML dans ``$actualXml`` n'est pas égal au document XML dans ``$expectedXml``.

``assertXmlStringNotEqualsXmlString()`` est l'inverse de cette assertion et prend les mêmes arguments.

.. code-block:: php
    :caption: Utilisation de assertXmlStringEqualsXmlString()
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.


