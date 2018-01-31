

.. _test-doubles:

================
Doublure de test
================

Gerard Meszaros introduit le concept de doublure de test dans
:ref:`Meszaros2007` comme ceci:

    *Gerard Meszaros*:

    Parfois il est parfaitement difficile de juste tester un système en cours de test (System Under Test : SUT)
    parce qu'il dépend d'autres composants qui ne peuvent pas être utilisés dans l'environnement de test.
    Ceci peut provenir du fait qu'ils ne sont pas disponibles, qu'ils ne retournent pas les résultats
    nécessaires pour les tests ou parce que les exécuter pourrait avoir des effets de bord indésirables.
    Dans d'autres cas, notre stratégie de test nécessite que nous ayons plus de contrôle ou de visibilité
    sur le comportement interne du SUT.

    Quand nous écrivons un test dans lequel nous ne pouvons pas (ou ne voulons pas)
    utiliser un composant réel dont on dépend (depended-on component ou DOC), nous
    pouvons le remplacer avec une doublure de test. La doublure de test ne se comporte pas exactement
    comme un vrai DOC; elle a simplement à fournir la même API que le composant réel de telle
    sorte que le système testé pense qu'il s'agit du vrai !

Les méthodes ``createMock($type)`` et
``getMockBuilder($type)`` fourni par PHPUnit peuvent être
utilisées dans un test pour générer automatiquement un objet qui peut agir comme une
doublure de test pour une classe originelle indiquée (interface ou non de classe).
Cette doublure de test peut être utilisée dans tous les contextes où la classe
originelle est attendue ou requise.

La méthode ``createMock($type)`` retourne immédiatement une doublure de
test pour le type spécifié (interface ou classe). La création de cette doublure est
effectuée en suivant par défaut les bonnes pratiques (les méthodes ``__construct()`` et ``__clone()`` de la classe originale ne sont pas exécutées
et les arguments passés à une méthode de la doublure de tests ne sont pas clonés. Si ce
comportement par défaut ne correspond pas à ce dont vous avez besoin vous pouvez
alors utiliser la méthode ``getMockBuilder($type)`` pour personnaliser la
génération de doublure de test en utilisant un interface souple (fluent interface).

Par défaut, toutes les méthodes de la classe originelle sont remplacées par une
implémentation fictive qui se contente de retourner ``null`` (sans
appeler la méthode originelle). En utilisant la méthode ``will($this->returnValue())``
par exemple, vous pouvez configurer ces implémentations fictives pour
retourner une valeur donnée quand elles sont appelées.

.. admonition:: Limitations: méthodes final, private et static

   Merci de noter que les méthodes ``final``, ``private``,
   ``protected`` et ``static`` ne peuvent pas être remplacées
   par un bouchon (stub) ou un mock. Elles seront ignorées par la
   fonction de doublure de test de PHPUnit et conserveront leur comportement initial.

.. _test-doubles.stubs:

Bouchons
########

La pratique consistant à remplacer un objet par une doublure de test qui
retourne (de façon facultative) des valeurs de retour configurées est
appelée *bouchonnage*. Vous pouvez utiliser un *bouchon* pour
"remplacer un composant réel dont dépend le système testé de telle façon que
le test possède un point de contrôle sur les entrées indirectes dans le SUT. Ceci
permet au test de forcer le SUT à utiliser des chemins qu'il n'aurait pas emprunté autrement".

:numref:`test-doubles.stubs.examples.StubTest.php` montre comment
la méthode de bouchonnage appelle et configure des valeurs de retour. Nous utilisons
d'abord la méthode ``createMock()`` qui est fournie par la classe
``PHPUnit\Framework\TestCase`` pour configurer un objet bouchon
qui ressemble à un objet de ``SomeClass``
(:numref:`test-doubles.stubs.examples.SomeClass.php`). Ensuite nous
utilisons `l'interface souple <http://martinfowler.com/bliki/FluentInterface.html>`_
que PHPUnit fournit pour indiquer le comportement de ce bouchon. En substance,
cela signifie que vous n'avez pas besoin de créer plusieurs objets temporaires
et les relier ensemble ensuite. Au lieu de cela, vous chaînez les appels de méthode
comme montré dans l'exemple. Ceci amène à un code plus lisible et "souple".

.. code-block:: php
    :caption: La classe que nous voulons bouchonner
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SomeClass
    {
        public function doSomething()
        {
            // Do something.
        }
    }
    ?>

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour retourner une valeur fixée
    :name: test-doubles.stubs.examples.StubTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Appeler $stub->doSomething() va maintenant retourner
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

.. admonition:: Limitation: Méthodes nommées "method"

   L'exemple ci-dessus ne fonctionne que si la classe originale ne déclare
   pas de méthode appelé "method".

   Si la classe originale déclare une methode appelée "method" alors vous devez utiliser ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');``.

"Dans les coulisses", PHPUnit génère automatiquement une nouvelle classe qui
implémente le comportement souhaité quand la méthode ``createMock()``
est utilisée.

:numref:`test-doubles.stubs.examples.StubTest2.php` montre un exemple
de comment utiliser l'interface souple du créateur de mock pour configurer
la création d'une doublure de test. La configuration de cette doublure de test
utilise les même bonnes pratiques utilisées par défaut par ``createMock()``.

.. code-block:: php
    :caption: L'API de construction des mocks peut être utilisée pour configurer la doublure de test générée.
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->getMockBuilder($originalClassName)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Appeler $stub->doSomething() retournera désormais
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

Dans les exemples précédents, nous avons retourné des valeurs simple en utilisant
``willReturn($value)``. Cette syntaxe courte est identique à
``will($this->returnValue($value))``. Nous pouvons utiliser des variantes
de cette syntaxte plus longue pour obtenir un comportement de bouchonnement plus complexe.

Parfois vous voulez renvoyer l'un des paramètres d'un appel de méthode
(non modifié) comme résultat d'un appel méthode bouchon.
:numref:`test-doubles.stubs.examples.StubTest3.php` montre comment vous
pouvez obtenir ceci en utilisant ``returnArgument()`` à la place de
``returnValue()``.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour renvoyer un des paramètres
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnArgumentStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') retourn 'foo'
            $this->assertEquals('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') returns 'bar'
            $this->assertEquals('bar', $stub->doSomething('bar'));
        }
    }
    ?>

Quand on teste interface souple, il est parfois utile que la méthode bouchon
retourne une référence à l'objet bouchon.
:numref:`test-doubles.stubs.examples.StubTest4.php` présente comment
utiliser ``returnSelf()`` pour accomplir cela.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour renvoyer une référence de l'objet bouchon.
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnSelf()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() retourne $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }
    ?>

Parfois, une méthode bouchon doit retourner différentes valeurs selon
une liste prédéfinie d'arguments. Vous pouvez utiliser
``returnValueMap()`` pour créer une association entre les
paramètres et les valeurs de retour correspondantes. Voir
:numref:`test-doubles.stubs.examples.StubTest5.php` pour
un exemple.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour retourner la valeur à partir d'une association
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnValueMapStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Créer une association entre arguments et valeurs de retour
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() retourne différentes valeurs selon
            // les paramètres fournis.
            $this->assertEquals('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertEquals('h', $stub->doSomething('e', 'f', 'g'));
        }
    }
    ?>

Quand l'appel d'une méthode bouchonné doit retourner une valeur calculée au lieu
d'une valeur fixée (voir ``returnValue()``) ou un paramètre
(non modifié) (voir ``returnArgument()``), vous pouvez utiliser
``returnCallback()`` pour que la méthode retourne le résultat
d'une fonction ou méthode de rappel. Voir
:numref:`test-doubles.stubs.examples.StubTest6.php` pour un exemple.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour retourner une valeur à partir d'une fonction de rappel
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnCallbackStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) retourne str_rot13($argument)
            $this->assertEquals('fbzrguvat', $stub->doSomething('something'));
        }
    }
    ?>

Une alternative plus simple pour configurer une méthode de rappel peut
consister à indiquer une liste de valeurs désirées. Vous pouvez faire
ceci avec la méthode ``onConsecutiveCalls()``. Voir
:numref:`test-doubles.stubs.examples.StubTest7.php` pour
un exemple.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour retourner une liste de valeurs dans l'ordre indiqué
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() retourne une valeur différente à chaque fois
            $this->assertEquals(2, $stub->doSomething());
            $this->assertEquals(3, $stub->doSomething());
            $this->assertEquals(5, $stub->doSomething());
        }
    }
    ?>

Au lieu de retourner une valeur, une méthode bouchon peut également lever
une exception. :numref:`test-doubles.stubs.examples.StubTest8.php`
montre comme utiliser ``throwException()`` pour faire cela.

.. code-block:: php
    :caption: Bouchonner un appel de méthode pour lever une exception
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testThrowExceptionStub()
        {
            // Créer un bouchon pour la classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configurer le bouchon.
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() throws Exception
            $stub->doSomething();
        }
    }
    ?>

Alternativement, vous pouvez écrire le bouchon vous-même et améliorer votre conception
en cours de route. Des ressources largement utilisées sont accédées via une unique façade,
de telle sorte que vous pouvez facilement remplacer la ressource avec le bouchon. Par exemple,
au lieu d'avoir des appels directs à la base de données éparpillés dans tout le code,
vous avez un unique objet ``Database``, une implémentation de
l'interface ``IDatabase``. Ensuite, vous pouvez créer
une implémentation bouchon de ``IDatabase`` et l'utiliser pour
vos tests. Vous pouvez même créer une option pour lancer les tests dans la
base de données bouchon ou la base de données réelle, de telle sorte que vous
pouvez utiliser vos tests à la fois pour tester localement pendant le développement
et en intégration avec la vraie base de données.

Les fonctionnalités qui nécessitent d'être bouchonnées tendent à se regrouper
dans le même objet, améliorant la cohésion. En représentant la fonctionnalité
avec une unique interface cohérente, vous réduisez le couplage avec le reste
du système.

.. _test-doubles.mock-objects:

Objets Mock
###########

La pratique consistant à remplacer un objet avec une doublure de test
qui vérifie des attentes, par exemple en faisant l'assertion qu'une méthode
a été appelée, est appelée *mock*.

Vous pouvez utiliser un *objet mock* "comme un point d'observation
qui est utilisé pour vérifier les sorties indirectes du système quand il est testé".
Typiquement, le mock inclut également la fonctionnalité d'un bouchon de test,
en ce sens qu'il doit retourner les valeurs du système testé s'il n'a pas déjà fait échouer
les tests mais l'accent est mis sur la vérification des sorties indirectes.
Ainsi, un mock est beaucoup plus qu'un simple bouchon avec des assertions;
il est utilisé d'une manière fondamentalement différente" (Gerard Meszaros).

.. admonition:: Limitation: Vérification automatique des attentes

   Seuls les objets mock générés dans le scope d'un test seront vérifiés
   automatiquement par PHPUnit. Les mocks générés dans les fournisseurs de données,
   par exemple, ou injectés dans les tests en utilisant l'annotation ``@depends``
   ne seront pas vérifiés automatiquement par PHPUnit.

Voici un exemple: supposons que vous voulez tester que la méthode correcte,
``update()`` dans notre exemple, est appelée d'un objet qui observe un autre objet.
:numref:`test-doubles.mock-objects.examples.SUT.php`
illustre le code pour les classes ``Subject`` et ``Observer``
qui sont une partie du système testé (SUT).

.. code-block:: php
    :caption: Les classes Subject et Observer qui sont une partie du système testé
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // Faire quelque chose.
            // ...

            // Notify les observateurs que nous faisons quelque chose
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // Autres méthodes.
    }

    class Observer
    {
        public function update($argument)
        {
            // Faire quelquechose
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // Faire quelquechose
        }

        // Autre méthodes
    }
    ?>

:numref:`test-doubles.mock-objects.examples.SubjectTest.php`
illustre comment utiliser un mock pour tester l'interaction entre
les objets ``Subject`` et ``Observer``.

Nous utilisons d'abord la méthode ``getMockBuilder()`` qui est fournie par
la classe ``PHPUnit\Framework\TestCase`` pour configurer un mock
pour ``Observer``. Puisque nous donnons un tableau comme second
paramètre (facultatif) pour la méthode ``getMock()``,
seule la méthode ``update()`` de la classe ``Observer`` est
remplacée par une implémentation d'un mock.

Comme ce qui nous intéresse est de vérifier qu'une méthode soit appelée,
et avec quels arguments, nous introduisons les méthodes ``expects()``
et ``with()`` pour spécifier comment cette interaction doit se présenter.

.. code-block:: php
    :caption: Tester qu'une méthode est appelée une fois et avec un paramètre indiqué
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            // Créer un mock pour la classe Observer,
            // ne touchant que la méthode update().
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['update'])
                             ->getMock();

            // Configurer l'attente de la méthode update()
            // d'être appelée une seule fois et avec la chaîne 'something'
            // comme paramètre.
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Créer un objet Subject et y attacher l'objet
            // Observer simulé
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Appeler la méthode doSomething() sur l'objet $subject
            // que nous attendons voir appeler la méthode update() de l'objet
            // simulé Observer avec la chaîne 'something'.
            $subject->doSomething();
        }
    }
    ?>

La méthode ``with()`` peut prendre n'importe quel
nombre de paramètres, correspondant au nombre de paramètres des méthodes
simulées. Vous pouvez indiquer des contraintes plus avancées qu'une simple
correspondance, sur les paramètres de méthode.

.. code-block:: php
    :caption: Tester qu'une méthode est appelée avec un nombre de paramètres contraints de différentes manières
    :name: test-doubles.mock-objects.examples.MultiParameterTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Créer un mock pour la classe Observer, en simulant
            // la méthode reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // La méthode doSomethingBad() doit rapporter une erreur à l'observateur
            // via la méthode reportError()
            $subject->doSomethingBad();
        }
    }
    ?>

La méthode ``withConsecutive()`` peut prendre n'importe quel
nombre de tableau de paramètres, selon les appels que vous souhaitez tester.
Chaque tableau est une liste de contraintes correspondant aux paramètres de la
méthode mockée, comme avec ``with()``.

.. code-block:: php
    :caption: Tester qu'une méthode est appelée deux fois avec des arguments spécifiques.
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments()
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }
    ?>

La contrainte ``callback()`` peut être utilisée pour une vérification
plus complexe d'un argument. Cette contrainte prend comme seul paramètre une fonction de
rappel PHP (callback). La fonctione de rappel PHP recevra l'argument à vérifier comme son seul
paramètre et devrait renvoyer ``true`` si l'argument
passe la vérification et ``false`` sinon.

.. code-block:: php
    :caption: Vérification de paramètre plus complexe
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Crée un mock pour la classe Observer, mock de la
            // méthode reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with($this->greaterThan(0),
                            $this->stringContains('Something'),
                            $this->callback(function($subject){
                              return is_callable([$subject, 'getName']) &&
                                     $subject->getName() == 'My subject';
                            }));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // La méthode doSomethingBad() devrait rapporter une erreur a l'observeur
            // via la methode reportError()
            $subject->doSomethingBad();
        }
    }
    ?>

.. code-block:: php
    :caption: Tester qu'une méthode est appelée une seule fois avec le même objet qui a été passé
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }
    ?>

.. code-block:: php
    :caption: Créer un OBJET mock avec les paramètres de clonage activés
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // maintenant votre mock clone les paramètres, ainsi la contrainte identicalTo
            // échouera.
        }
    }
    ?>

:ref:`appendixes.assertions.assertThat.tables.constraints`
montre les contraintes qui peuvent être appliquées aux paramètres de méthode et
:numref:`test-doubles.mock-objects.tables.matchers`
montre les matchers qui sont disponibles pour indiquer le nombre
d'invocations.

.. rst-class:: table
.. list-table:: Matchers
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - Matcher
      - Signification
    * - ``PHPUnit_Framework_MockObject_Matcher_AnyInvokedCount any()``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué est exécutée zéro ou davantage de fois.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount never()``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué n'est jamais exécutée.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedAtLeastOnce atLeastOnce()``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué est exécutée au moins une fois.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount once()``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué est exécutée exactement une fois.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount exactly(int $count)``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué est exécutée exactement ``$count`` fois.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedAtIndex at(int $index)``
      - Retourne un matcher qui correspond quand la méthode pour laquelle il est évalué est invoquée pour l'``$index`` spécifié.

.. admonition:: Note

   Le paramètre ``$index`` du matcher ``at()``
   fait référence à l'index, démarrant à zero, dans *toutes les invocations de
   la méthode* pour un objet mock. Faites preuve de prudence
   lors de l'utilisation de ce matcher car cela peut conduire à des tests
   fragiles qui seront trop étroitement liés aux détails d'implémentation spécifiques.

Comme mentionné au début, quand le comportement par défaut utilisé par la méthode
``createMock()`` pour générer la doublure de test ne
correspond pas a vos besoins alors vous pouvez utiliser la méthode ``getMockBuilder($type)``
pour personnaliser la génération de la doublure de test en utilisant une interface souple.
Voici une liste des méthodes fournies par le constructeur de mock:

-

  ``setMethods(array $methods)`` peut être appelé sur l'objet Mock Builder pour spécifier les méthodes qui doivent être remplacées par une doublure de test configurable. Le comportement des autres méthodes n'est pas changé. Si vous appelez ``setMethods(null)``, alors aucune méthode ne sera remplacé.

-

  ``setMethodsExcept(array $methods)`` peut être appelé sur l'objet Mock Builder pour spécifier les méthodes qui ne seront pas remplacées par un double de test configurable lors du remplacement de toutes les autres méthodes publiques. Cela fonctionne à l'inverse de ``setMethods()``.

-

  ``setConstructorArgs(array $args)`` peut être appelé pour fournir un tableau de paramètres qui est passé au constructeur de la classe originale (qui n'est pas remplacé par une implémentation factice par défaut).

-

  ``setMockClassName($name)`` peut être utilisé pour spécifier un nom de classe pour la classe de la doublure de test générée.

-

  ``disableOriginalConstructor()`` peut être utilisé pour désactiver l'appel au constructeur de la classe originale.

-

  ``disableOriginalClone()`` peut être utilisé pour désactiver l'appel au constructeur de clonage de la classe originale.

-

  ``disableAutoload()`` peut être utilisé pour désactiver ``__autoload()`` pendant la génération de la classe de la doublure de test.

.. _test-doubles.prophecy:

Prophecy
########

`Prophecy <https://github.com/phpspec/prophecy>`_ est un
"framework de simulation d'objets PHP fortement arrêtée dans ses options mais tout
du moins très puissant et flexible. Bien qu'il ait été initialement créé pour satisfaire les besoins de phpspec2, il est
suffisamment souple pour être utilisé dans n'importe quel framework de test avec un
minimum d'effort".

PHPUnit dispose d'un support intégré pour utiliser Prophecy pour créer des doublures de test.
:numref:`test-doubles.prophecy.examples.SubjectTest.php`
montre comment le même test montré dans :numref:`test-doubles.mock-objects.examples.SubjectTest.php`
peut être exprimé en utilisant la philosophie de Prophecy de prophéties et de
révélations:

.. code-block:: php
    :caption: Tester qu'une méthode est appelée une fois et avec un paramètre indiqué
    :name: test-doubles.prophecy.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            $subject = new Subject('My subject');

            // Crée une prophecy pour la classe Observer.
            $observer = $this->prophesize(Observer::class);

            // Configure l'attente pour que la méthode update()
            // soit appelée une seule fois avec la chaine 'something'
            // en paramètre.
            $observer->update('something')->shouldBeCalled();

            // Révèle la prophecie et attache l'objet mock
            // à $subjet
            $subject->attach($observer->reveal());

            // Appelle la méthode doSomething() sur l'objet $subject
            // dont on s'attend a ce qu'il appelle la méthode update()l'objet mocké Observer
            // avec la chaine 'something'.
            $subject->doSomething();
        }
    }
    ?>

Reportez-vous à la `documentation <https://github.com/phpspec/prophecy#how-to-use-it>`_
de Prophecy pour plus de détails sur la création, la configuration et l'utilisation de
stubs, espions, et mocks en utilisant ce framework alternatif de doublure de test.

.. _test-doubles.mocking-traits-and-abstract-classes:

Mocker les Traits et les classes abstraites
###########################################

La méthode ``getMockForTrait()`` renvoie un objet mock
qui utilise un Trait spécifié. Toutes les méthodes abstraites du Trait donné
sont mockées. Cela permet de tester les méthodes concrètes d'un Trait.

.. code-block:: php
    :caption: Tester les méthodes concrètes d'un trait
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class TraitClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }
    ?>

La méthode ``getMockForAbstractClass()`` retourne un mock
pour une classe abstraite. Toutes les méthodes abstraites d'une classe mock
donnée sont simulées. Ceci permet de tester les méthodes concrètes d'une classe
abstraite.

.. code-block:: php
    :caption: Tester les méthodes concrètes d'une classe abstraite
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }
    ?>

.. _test-doubles.stubbing-and-mocking-web-services:

Bouchon et mock pour Web Services
######################################

Quand votre application interagit avec un web service, vous voulez le
tester sans vraiment interagir avec le web service. Pour rendre facile
la création de bouchon ou de mock de web services, ``getMockFromWsdl()``
peut être utilisée de la même façon que ``getMock()`` (voir plus haut). La seule
différence est que ``getMockFromWsdl()`` retourne un bouchon ou un mock
basé sur la description en WSDL d'un web service tandis que ``getMock()``
retourne un bouchon ou un mock basé sur une classe ou une interface PHP.

:numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php`
montre comment ``getMockFromWsdl()`` peut être utilisé pour faire un bouchon,
par exemple, d'un web service décrit dans :file:`GoogleSearch.wsdl`.

.. code-block:: php
    :caption: Bouchonner un web service
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GoogleTest extends TestCase
    {
        public function testSearch()
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() will now return a stubbed result and
             * the web service's doGoogleSearch() method will not be invoked.
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }
    ?>

.. _test-doubles.mocking-the-filesystem:

Simuler le système de fichiers
##############################

`vfsStream <https://github.com/mikey179/vfsStream>`_
est un `encapsuleur de flux <http://www.php.net/streams>`_ pour un
`système de fichiers
virtuel <http://en.wikipedia.org/wiki/Virtual_file_system>`_ qui peut s'avérer utile dans des tests unitaires pour simuler
le vrai système de fichiers.

Ajoutez simplement une dépendance a ``mikey179/vfsStream`` dans le
fichier ``composer.json`` de votre projet si vous utilisez
`Composer <https://getcomposer.org/>`_ pour gérer les
dépendances de votre project. Vous trouverez ci-dessous un exemple minimal de fichier
``composer.json`` qui définie en dépendance de développement
PHPUnit 4.6 et vfsStream:

.. code-block:: php

    {
        "require-dev": {
            "phpunit/phpunit": "~4.6",
            "mikey179/vfsStream": "~1"
        }
    }

:numref:`test-doubles.mocking-the-filesystem.examples.Example.php`
montre une classe qui interagit avec le système de fichiers.

.. code-block:: php
    :caption: Une classe qui interagit avec le système de fichiers
    :name: test-doubles.mocking-the-filesystem.examples.Example.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Example
    {
        protected $id;
        protected $directory;

        public function __construct($id)
        {
            $this->id = $id;
        }

        public function setDirectory($directory)
        {
            $this->directory = $directory . DIRECTORY_SEPARATOR . $this->id;

            if (!file_exists($this->directory)) {
                mkdir($this->directory, 0700, true);
            }
        }
    }?>

Sans un système de fichiers virtuel tel que vfsStream, nous ne pouvons
pas tester la méthode ``setDirectory()`` en isolation des influences
extérieures (voir :numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest.php`).

.. code-block:: php
    :caption: Tester une classe qui interagit avec le système de fichiers
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        protected function setUp()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(file_exists(dirname(__FILE__) . '/id'));

            $example->setDirectory(dirname(__FILE__));
            $this->assertTrue(file_exists(dirname(__FILE__) . '/id'));
        }

        protected function tearDown()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }
    }
    ?>

L'approche précédente possède plusieurs inconvénients :

-

  Comme avec les ressources externes, il peut y a voir des problèmes intermittents avec le système de fichiers. Ceci rend les tests qui interagissent avec lui peu fiables.

-

  Dans les méthodes ``setUp()`` et ``tearDown()``, nous avons à nous assurer que le répertoire n'existe pas avant et après le test.

-

  Si l'exécution du test s'achève avant que la méthode ``tearDown()`` n'ait été appelée, le répertoire va rester dans le système de fichiers.

:numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest2.php`
montre comment vfsStream peut être utilisé pour simuler le système de fichiers dans un test
pour une classe qui interagit avec le système de fichiers.

.. code-block:: php
    :caption: Simuler le système de fichiers dans un test pour une classe qui interagit avec le système de fichiers
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        public function setUp()
        {
            vfsStreamWrapper::register();
            vfsStreamWrapper::setRoot(new vfsStreamDirectory('exampleDir'));
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(vfsStreamWrapper::getRoot()->hasChild('id'));

            $example->setDirectory(vfsStream::url('exampleDir'));
            $this->assertTrue(vfsStreamWrapper::getRoot()->hasChild('id'));
        }
    }
    ?>

Ceci présente plusieurs avantages :

-

  Le test lui-même est plus concis.

-

  vfsStream donne au développeur du test le plein contrôle sur la façon dont le code testé voit l'environnement du système de fichiers.

-

  Puisque les opérations du système de fichiers n'opèrent plus sur le système de fichiers réel, les opérations de nettoyage dans la méthode ``tearDown()`` ne sont plus nécessaires.


