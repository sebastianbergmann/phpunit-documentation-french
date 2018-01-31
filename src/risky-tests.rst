

.. _risky-tests:

=============
Tests risqués
=============

PHPUnit peut effectuer les vérifications supplémentaires documentées ci-dessous pendant qu'il exécute
les tests.

.. _risky-tests.useless-tests:

Tests inutiles
##############

PHPUnit est par défaut strict sur les tests qui ne testent rien. Cette vérification
peut être désactivée en utilisant l'option ``--dont-report-useless-tests``
de la ligne de commande ou en définissant
``beStrictAboutTestsThatDoNotTestAnything="false"`` dans
le fichier de configuration XML de PHPUnit.

Un test qui n'effectue pas d'assertion sera marqué comme risqué
lorsque cette vérification est activée. Les attentes sur les objets bouchonnés ou les annotations
telles que @expectedException comptent comme une assertion.

.. _risky-tests.unintentionally-covered-code:

Code non-intentionnellement couvert
###################################

PHPUnit peut être strict sur le code couvert non-intentionnellement. Cette vérification
peut être activée en utilisant l'option ``--strict-coverage``
de la ligne de commande ou en définissant
``beStrictAboutCoversAnnotation="true"`` dans
le fichier de configuration XML de PHPUnit.

Un test qui est annoté avec @covers et exécute du code qui
n'est pas listé avec les annotations @covers ou @uses
sera marqué comme risqué quand cette vérification est activée.

.. _risky-tests.output-during-test-execution:

Sortie d'écran lors de l'exécution d'un test
############################################

PHPUnit peut être strict sur la sortie écran pendant les tests. Cette vérification
peut être activée en utilisant l'option ``--disallow-test-output``
de la ligne de commande ou en définissant
``beStrictAboutOutputDuringTests="true"`` dans
le fichier de configuration XML de PHPUnit.

Un test qui émet une sortie écran, par exemple en appelant print
dans le code du test ou dans le code testé, sera marqué comme risqué quand
cette vérification est activée.

.. _risky-tests.test-execution-timeout:

Délai d'exécution des tests
###########################

Une limite de temps peut être appliquée pour l'exécution d'un test si le
paquet ``PHP_Invoker`` est installé et que
l'extension ``pcntl`` est disponible. L'application de cette
limite de temps peut être activée en utilisant
l'option ``--enforce-time-limit`` sur la ligne de commande ou en
définissant ``beStrictAboutTestSize="true"`` dans le fichier de
configuration XML de PHPUnit.

Un test annoté avec ``@large`` échouera s'il prend
plus de 60 secondes à s'exécuter. Ce délai d'exécution est configurable via l'attribut
``timeoutForLargeTests`` dans le fichier
de configuration XML.

Un test annoté avec ``@medium`` échouera s'il prend
plus de 10 secondes à s'exécuter. Ce délai d'exécution est configurable via l'attribut
``timeoutForMediumTests`` dans le fichier
de configuration XML.

Un test qui n'est pas annoté avec ``@medium`` ou
``@large`` sera traité comme s'il était annoté avec
``@small``. Un test "small" échouera s'il prend
plus de 1 seconde à s'exécuter. Ce délai d'exécution est configurable via
l'attribut ``timeoutForMediumTests`` dans le fichier de
configuration XML.

.. _risky-tests.global-state-manipulation:

Manipulation d'états globaux
############################

PHPUnit peut être strict sur les tests qui manipulent l'état global. Cette vérification
peut être activé en utilisant l'option ``--strict-global-state``
de la ligne de commande ou en définissant
``beStrictAboutChangesToGlobalState="true"`` dans le fichier de
configuration XML de PHPUnit.


