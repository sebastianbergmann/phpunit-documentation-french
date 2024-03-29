

.. _appendixes.annotations:

===========
Annotations
===========

Une annotation est une forme spéciale de métadonnée syntaxique qui peut
être ajoutée au code source de certains langages de programmation. Bien que
PHP n'ait pas de fonctionnalité dédiée à l'annotation du code source, l'utilisation
d'étiquettes telles que ``@annotation arguments`` dans les blocs de documentation
s'est établi dans la communauté PHP pour annoter le code source. En PHP, les blocs de
documentation sont réflexifs : ils peuvent être accédés via la méthode de l'API de réflexivité
``getDocComment()`` au niveau des fonctions, classes, méthodes et attributs.
Des applications telles que PHPUnit utilisent ces informations durant l'exécution pour
adapter leur comportement.

.. admonition:: Note

   Un "doc comment" en PHP doit commencer par ``/**`` et se terminer avec
   ``*/``. Les annotations se trouvant dans des commentaires d'un autre style
   seront ignorées.

Cette annexe montre toutes les sortes d'annotations gérées par PHPUnit.

.. _appendixes.annotations.author:

@author
#######

L'annotation ``@author`` est un alias pour l'annotation
``@group`` (voir :ref:`appendixes.annotations.group`) et permet de filtrer des tests selon
leurs auteurs.

.. _appendixes.annotations.after:

@after
######

L'annotation ``@after`` peut être utilisée pour spécifier des méthodes
devant être appelées après chaque méthode de test dans une classe de cas de tests.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

L'annotation ``@afterClass`` peut être utilisée pour spécifier
des méthodes statiques devant être appelées après chaque méthode de test
dans une classe de test pour être exécuté afin de nettoyer des fixtures partagées.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

Les opérations de sauvegarde et de restauration des variables globales peuvent
être complètement désactivées pour tous les tests d'une classe de cas de test comme ceci :

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        // ...
    }

L'annotation ``@backupGlobals`` peut également être
utilisée au niveau d'une méthode. Cela permet une configuration fine
des opérations de sauvegarde et de restauration :

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

L'annotation ``@backupStaticAttributes`` peut être utilisée pour
enregistrer tous les attributs statiques dans toutes les classes déclarées
avant chaque test et les restaurer après. Elle peut être utilisée au niveau de la classe ou
au niveau de la méthode :

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

.. admonition:: Note

   ``@backupStaticAttributes`` est limitée par le fonctionnement interne de PHP
   et peut entrainer la persistance inattendue de valeurs statique et fuiter dans
   les tests suivants tests dans certaines circonstances.

   Voir :ref:`fixtures.global-state` pour les détails.

.. _appendixes.annotations.before:

@before
#######

L'annotation ``@before`` peut être utilisée pour spécifier des méthodes
devant être appelées avant chaque méthode de test dans une classe de cas de test.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

L'annotation ``@beforeClass`` peut être utilisée pour spécifier
des méthodes statiques qui doivent être appelées avant chaque méthode de test dans une classe
de test qui sont exécutés pour paramétrer des fixtures partagées.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

Les annotations ``@codeCoverageIgnore``,
``@codeCoverageIgnoreStart`` et
``@codeCoverageIgnoreEnd`` peuvent être utilisées pour
exclure des lignes de code de l'analyse de couverture.

Pour la manière de les utiliser, voir :ref:`code-coverage-analysis.ignoring-code-blocks`.

.. _appendixes.annotations.covers:

@covers
#######

L'annotation ``@covers`` peut être utilisée dans le code de test pour
indique quelle(s) méthode(s) une méthode de test veut tester:

.. code-block:: php

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

Si elle est fournie, seule l'information de couverture de code pour
la(les) méthode(s) sera prise en considération.

:numref:`appendixes.annotations.covers.tables.annotations` montre
la syntaxe de l'annotation ``@covers``.

.. rst-class:: table
.. list-table:: Annotations pour indiquer quelles méthodes sont couvertes par un test
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - Annotation
      - Description
    * - ``@covers ClassName::methodName``
      - Indique que la méthode de test annotée couvre la méthode indiquée.
    * - ``@covers ClassName``
      - Indique que la méthode de test annotée couvre toutes les méthodes d'une classe donnée.
    * - ``@covers ClassName<extended>``
      - Indique que la méthode de test annotée couvre toutes les méthodes d'une classe donnée ainsi que les classe(s) et interface(s) parentes.
    * - ``@covers ClassName::<public>``
      - Indique que la méthode de test annotée couvre toutes les méthodes publiques d'une classe donnée.
    * - ``@covers ClassName::<protected>``
      - Indique que la méthode de test annotée couvre toutes les méthodes protected d'une classe donnée.
    * - ``@covers ClassName::<private>``
      - Indique que la méthode de test annotée couvre toutes les méthodes privées d'une classe donnée.
    * - ``@covers ClassName::<!public>``
      - Indique que la méthode de test annotée couvre toutes les méthodes d'une classe donnée qui ne sont pas publiques.
    * - ``@covers ClassName::<!protected>``
      - Indique que la méthode de test annotée couvre toutes les méthodes d'une classe donnée qui ne sont pas protected.
    * - ``@covers ClassName::<!private>``
      - Indique que la méthode de test annotée couvre toutes les méthodes d'une classe donnée qui ne sont pas privées.
    * - ``@covers ::functionName``
      - Indique que la méthode de test annotée couvre la méthode globale spécifiée.

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

L'annotation ``@coversDefaultClass`` peut être utilisée pour
spécifier un espace de nom ou un nom de classe par défaut. Ainsi, les noms long n'ont pas besoin d'être
répétés pour chaque annotation ``@covers``. Voir
:numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`.

.. code-block:: php
    :caption: Utiliser @coversDefaultClass pour simplifier les annotations
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }


.. _appendixes.annotations.coversNothing:

@coversNothing
##############

L'annotation ``@coversNothing`` peut être utilisée dans le code de test
pour indiquer qu'aucune information de couverture de code ne sera enregistrée pour le
cas de test annoté.

Ceci peut être utilisé pour les tests d'intégration. Voir
:ref:`code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php`
pour un exemple.

L'annotation peut être utilisée au niveau de la classe et de la méthode
et sera surchargée par toute étiquette ``@covers``.

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

Une méthode de test peut accepter des paramètres arbitraires. Ces paramètres
peuvent être fournis par une ou plusieurs méthodes fournisseuses de données ((``provider()`` dans
:ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
La méthode fournisseur de données peut être indiquée en utilisant l'annotation
``@dataProvider``.

Voir :ref:`writing-tests-for-phpunit.data-providers` pour plus de
détails.

.. _appendixes.annotations.depends:

@depends
########

PHPUnit gère la déclaration des dépendances explicites entre les méthodes
de test. De telles dépendances ne définissent pas l'ordre dans lequel les
méthodes de test doivent être exécutées, mais elles permettent de retourner
l'instance d'une fixture de test par un producteur et de la passer aux consommateurs dépendants.
:ref:`writing-tests-for-phpunit.examples.StackTest2.php` montre
comment utiliser l'annotation ``@depends`` pour exprimer des
dépendances entre méthodes de test.

Voir :ref:`writing-tests-for-phpunit.test-dependencies` pour plus de
détails.

.. _appendixes.annotations.doesNotPerformAssertions:

@doesNotPerformAssertions
#########################

Spécifie que le test n'effectue aucune assertion, il ne sera donc pas marqué comme risqué.

.. _appendixes.annotations.group:

@group
######

Un test peut être marqué comme appartement à un ou plusieurs groupes en utilisant
l'annotation ``@group`` comme ceci

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

L'annotation ``@group`` peut également être mise sur la classe
de test. Elle est ensuite "héritée" par toutes les méthodes de test de cette classe de test.

Des tests peuvent être sélectionnés pour l'exécution en se basant sur les groupes
en utilisant les options ``--group`` et ``--exclude-group``
du lanceur de test en ligne de commandes ou en utilisant les directives respectives du
fichier de configuration XML.

.. _appendixes.annotations.large:

@large
######

L'annotation ``@large`` est un alias pour
``@group large``.

Si le paquet ``PHP_Invoker`` est installé et que le mode strict
est activé, un test "large" échouera s'il prend plus de 60
secondes pour s'exécuter. Ce délai est configurable via l'attribut
``timeoutForLargeTests`` dans le fichier de
configuration XML.

.. _appendixes.annotations.medium:

@medium
#######

L'annotation ``@medium`` est un alias pour
``@group medium``. Un test "medium" ne doit pas dépendre d'un test
marqué comme ``@large``.

Si le paquet ``PHP_Invoker`` est installé et que le mode strict
est activé, un test "medium" échouera s'il prend plus de 10
secondes pour s'exécuter. Ce délai est configurable via l'attribut
``timeoutForMediumTests`` dans le fichier de
configuration XML.

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

Quand un test est exécuté dans un processus séparé, PHPUnit va
tenter de conserver l'état global du processus parent en
sérialisant toutes les globales dans le processus parent et en les désérialisant
dans le processus enfant. Cela peut poser des problèmes si le processus parent
contient des globales qui ne sont pas sérialisable. Pour corriger cela, vous pouvez empêcher
PHPUnit de conserver l'état global avec
l'annotation ``@preserveGlobalState``.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

L'annotation ``@requires`` peut être utilisée pour sauter des tests lorsque
des pré-requis communs, comme la version de PHP ou des extensions installées, ne sont pas fournis.

Une liste complète des possibilités et des exemples peuvent être trouvés à
:ref:`incomplete-and-skipped-tests.requires.tables.api`

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

Indique que tous les tests d'une classe de tests doivent être exécutés dans
un processus PHP séparé.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends TestCase
    {
        // ...
    }

*Note:* Par défaut, PHPUnit va
essayer de conserver l'état global depuis le processus parent en
sérialisant toutes les globales dans le processus parent et en les désérialisant
dans le processus enfant. Cela peut poser des problèmes si le processus parent
contient des globales qui ne sont pas sérialisable. Voir :ref:`appendixes.annotations.preserveGlobalState` pour plus d'information
sur comment le corriger.

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

Indique qu'un test doit être exécuté dans un processus PHP séparé.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

*Note:* Par défaut, PHPUnit va
essayer de conserver l'état global depuis le processus parent en
sérialisant toutes les globales dans le processus parent et en les désérialisant
dans le processus enfant. Cela peut poser des problèmes si le processus parent
contient des globales qui ne sont pas sérialisable. Voir :ref:`appendixes.annotations.preserveGlobalState` pour plus d'information
sur comment le corriger.

.. _appendixes.annotations.small:

@small
######

L'annotation ``@small`` est un alias pour
``@group small``. Un test "small" ne doit pas dépendre d'un test
marqué comme ``@medium`` ou ``@large``.

Si le paquet ``PHP_Invoker`` est installé et que le mode strict
est activé, un test "small" va échouer s'il prend plus d'1
seconde pour s'exécuter. Ce délai est configurable via
l'attrubut ``timeoutForSmallTests`` dans le fichier
de configuration XML.

.. admonition:: Note

   Les tests doivent être explicitement annotés par soit ``@small``,
   ``@medium`` ou ``@large`` pour activer les temps limites d'exécution.

.. _appendixes.annotations.test:

@test
#####

Comme alternative à préfixer vos noms de méthodes de test avec
``test``, vous pouvez utiliser l'annotation ``@test``
dans le bloc de documentation d'une méthode pour la marquer comme méthode de test.

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

Spécifie une description alternative utilisée lors de la génération des phrases de la
documentation agile.

L'annotation ``@testdox`` peut être appliqué aux classes de tests et aux méthodes.

.. code-block:: php

    /**
     * @testdox A bank account
     */
    class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: Note

    Avant PHPUnit 7.0 (en raison d'un bug dans l'analyse des annotations), utiliser
    l'annotation ``@testdox`` active aussi le comportement
    de l'annotation ``@test``.

.. _appendixes.annotations.testWith:

@testWith
#########

Au lieu d'implémenter une méthode à utiliser avec ``@dataProvider``,
vous pouvez définir un jeu de données en utilisant l'annotation ``@testWith``.

Un jeu de données comprend un ou plusieurs éléments. Pour définir un jeu de données
avec plusieurs éléments, définissez chaque élément dans une ligne distincte.
Chaque élément de l'ensemble de données doit être un tableau défini en JSON.

Voir :ref:`writing-tests-for-phpunit.data-providers` pour en apprendre
plus sur comment passer un jeu de données à un test.

.. code-block:: php

    /**
     * @param string    $input
     * @param int       $expectedLength
     *
     * @testWith        ["test", 4]
     *                  ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength)
    {
        $this->assertSame($expectedLength, strlen($input));
    }

La représentation d'un objet en JSON sera convertie en tableau associatif.

.. code-block:: php

    /**
     * @param array     $array
     * @param array     $keys
     *
     * @testWith        [{"day": "monday", "conditions": "sunny"}, ["day", "conditions"]]
     */
    public function testArrayKeys($array, $keys)
    {
        $this->assertSame($keys, array_keys($array));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

L'annotation ``@ticket`` est un alias pour l'annotation
``@group``  (voir :ref:`appendixes.annotations.group`) et permet de filtrer des tests selon
leurs identifiants de tickets.

.. _appendixes.annotations.uses:

@uses
#####

L'annotation ``@uses`` spécifie du code qui sera exécuté
par un test, mais qui n'est pas destiné à être couvert par le test. Un bon
exemple est un objet-valeur qui est nécessaire pour tester une partie du code.

.. code-block:: php

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

Cette annotation est particulièrement utile en mode de couverture stricte où
du code involontairement couvert va faire échouer un test. Voir
:ref:`risky-tests.unintentionally-covered-code` pour plus
d'informations sur le mode de couverture stricte.
