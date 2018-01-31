

.. _database:

===========================
Tester des bases de données
===========================

De nombreux exemples de tests unitaires de niveau débutant ou intermédiaire
dans de nombreux langages de programmation suggèrent qu'il est parfaitement facile de tester la logique de votre application avec
de simples tests. Pour les applications centrées sur une base de données, c'est loin d'être la
réalité. Commencez à utiliser WordPress, TYPO3 ou Symfony avec Doctrine ou Propel, par
exemple, et nous serez vite confrontés à des problèmes considérables avec PHPUnit :
juste parce que la base de données est vraiment étroitement liée à ces bibliothèques.

.. admonition:: Note

   Assurez-vous d'avoir l'extension PHP ``pdo`` et les extensions
   spécifique à la base de données tel que ``pdo_mysql`` installées.
   Sinon, les exemples ci-dessous ne fonctionneront pas.

Vous connaissez probablement ce scénario rencontré tous les jours sur les projets,
dans lequel vous voulez mettre à l'oeuvre votre savoir-faire tout neuf ou déjà aguerri en
PHPUnit et où vous vous retrouvez bloqué par l'un des problèmes suivants :

#.

   La méthode que vous voulez tester exécute une opération JOIN plutôt
   vaste et utilise les données pour calculer certains résultats importants.

#.

   Votre logique métier exécute un mélange d'instructions SELECT, INSERT, UPDATE et
   DELETE.

#.

   Vous devez configurer les données de test dans (éventuellement beaucoup) plus
   de deux tables pour obtenir des données initiales raisonnables pour les méthodes que vous voulez tester.

L'extension DbUnit simplifie considérablement la configuration d'une base de données à des fins
de test et vous permet de vérifier le contenu d'une base de données après avoir
réalisé une suite d'opérations.

.. _database.supported-vendors-for-database-testing:

Systèmes gérés pour tester des bases de données
###############################################

DbUnit gère actuellement MySQL, PostgreSQL, Oracle et SQLite. Via
l'intégration de `Zend Framework <http://framework.zend.com>`_ ou de
`Doctrine 2 <http://www.doctrine-project.org>`_
il est possible d'accéder à d'autres systèmes de base de données comme IBM DB2 ou
Microsoft SQL Server.

.. _database.difficulties-in-database-testing:

Difficultés pour tester les bases de données
############################################

Il y a une bonne raison pour laquelle les exemples concernant le test unitaire
n'inclut pas d'interaction avec une base de données : ces types de test sont à la
fois complexes à configurer et à maintenir. Quand vous faites des tests sur votre
base de données, vous devez prendre soin des variables suivantes :

-

  Le schéma et les tables de la base de données

-

  Insérer les lignes nécessaires pour le test dans ces tables

-

  Vérifier l'état de la base de données après que votre test a été exécuté

-

  Nettoyer la base de données pour chaque nouveau test

Comme de nombreuses APIs de base de données comme PDO, MySQLi ou OCI8 sont
lourdes à utiliser et verbeuses à écrire, réaliser ces étapes à la main
est un cauchemar absolu.

Le code de test doit être aussi court et précis que possible pour plusieurs raisons :

-

  Vous ne voulez pas modifier un volume considérable de code de test pour
  de petites modifications dans votre code de production.

-

  Vous voulez être capable de lire et de comprendre le code de test
  facilement, même des mois après l'avoir écrit.

De plus, vous devez prendre conscience que la base de données est essentiellement
une variable globale pour votre code. Deux tests de votre série de tests
peuvent être exécutés sur la même base de données, potentiellement en réutilisant les données
plusieurs fois. Un échec dans un test peut facilement affecter le résultat des tests suivants
rendant votre expérimentation de test très difficile. L'étape de nettoyage mentionnée précédemment
est d'une importance majeure pour résoudre le problème posé par le fait que
“la base de données est une variable globale“.

DbUnit aide à simplifier tous ces problèmes avec le test de base de données
d'une manière élégante.

Là où PHPUnit ne peut pas vous aider c'est pour le fait que les tests
de base de données sont très lents comparés aux tests n'en utilisant pas. Selon
l'importance des interactions avec votre base de données, vos tests
peuvent s'exécuter sur une durée considérable. Cependant, si vous gardez petit le volume de
données utilisées pour chaque test et que vous essayez de tester le plus de code possible en utilisant
des tests qui ne font pas appel à une base de données, vous pouvez facilement rester très en dessous
d'une minute, même pour de grandes séries de tests.

La suite de test du `projet Doctrine 2 <http://www.doctrine-project.org>`_, par exemple, possède actuellement une suite de tests d'environ 1000 tests
dont presque la moitié accède à la base de données et continue à s'exécuter en 15
secondes sur une base de données MySQL sur un ordinateur de
bureau standard.

.. _database.the-four-stages-of-a-database-test:

Les quatre phases d'un test de base de données
##############################################

Dans son livre sur les patterns de tests xUnit, Gerard Meszaros liste les
quatre phases d'un test unitaire :

#.

   Configurer une fixture

#.

   Expérimenter le système à tester

#.

   Vérifier les résultats

#.

   Nettoyer

    *Qu'est-ce qu'une fixture ?*

    Une fixture décrit l'état initial dans lequel se trouvent votre application
    et votre base de données quand vous exécutez un test.

Tester la base de données nécessite au moins d'intervenir dans
setup et teardown pour nettoyer et écrire les données de fixture
nécessaires dans vos tables. Cependant, l'extension de base de données possède une bonne raison
de rétablir les quatre phases dans un test de base de données pour constituer le processus suivant
qui est exécuté pour chacun des tests :

.. _database.clean-up-database:

1. Nettoyer la base de données
==============================

Puisqu'il y a toujours un premier test qui s'exécute en faisant
appel à la base de données, vous n'êtes pas sûr qu'il y ait déjà
des données dans les tables. PHPUnit va exécuter un TRUNCATE sur toutes
les tables que vous avez indiquées pour les remettre à l'état vide.

.. _database.set-up-fixture:

2. Configurer les fixtures
==========================

PHPUnit va parcourir toutes les lignes de fixture indiquées et les
insérer dans leurs tables respectives.

.. _database.run-test-verify-outcome-and-teardown:

3–5. Exécuter les tests, vérifier les résultats et nettoyer
===========================================================

Une fois la base de données réinitialisée et remise dans son état de départ,
le test en tant que tel est exécuté par PHPUnit. Cette partie du code de test
ne nécessite pas du tout de s'occuper de l'extension base de données,
vous pouvez procéder et tester tout ce que vous voulez dans votre code.

Votre test peut utiliser une assertion spéciale appelée
``assertDataSetsEqual()`` à des fins de vérification,
mais c'est totalement facultatif. Cette fonctionnalité sera expliquée
dans la section “Assertions pour les bases de données“.

.. _database.configuration-of-a-phpunit-database-testcase:

Configuration d'un cas de test de base de données PHPUnit
#########################################################

Habituellement quand vous utilisez PHPUnit, vos cas de tests devraient
hériter de la classe
``PHPUnit\Framework\TestCase`` de la façon suivante :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        public function testCalculate()
        {
            $this->assertEquals(2, 1 + 1);
        }
    }
    ?>

Si vous voulez tester du code qui fonctionne avec l'extension base de données,
le setup sera un peu plus complexe et vous devrez hériter d'un cas de test
abstrait différent qui nécessite que vous implémentiez deux méthodes abstraites
``getConnection()`` et
``getDataSet()``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit_Extensions_Database_DataSet_IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }
    ?>

.. _database.implementing-getconnection:

Implémenter getConnection()
===========================

Pour permettre aux fonctionnalités de nettoyage et de chargement des fixtures de fonctionner,
l'extension de base de données PHPUnit nécessite d'accéder à une connexion de base de données
abstraite pour les différents fournisseurs via la bibliothèque PDO. Il est important de noter
que votre application n'a pas besoin de s'appuyer sur PDO pour utiliser
l'extension de base de données de PHPUnit, la connexion est
principalement utilisée pour le nettoyage et la configuration de setup.

Dans l'exemple précédent, nous avons créé une connexion Sqlite en mémoire
et nous l'avons passé à la méthode ``createDefaultDBConnection``
qui encapsule l'instance PDO et le second paramètre (le
nom de la base de données) dans une couche d'abstraction très simple pour connexion
aux bases de données du type
``PHPUnit_Extensions_Database_DB_IDatabaseConnection``.

La section “Utiliser l'API la connexion de base de données“ explicite l'API
de cette interface et comment en faire le meilleur usage.

.. _database.implementing-getdataset:

Implémenter getDataSet()
========================

La méthode ``getDataSet()`` définit à quoi doit ressembler
l'état initial de la base de données avant que chaque test ne soit
exécuté. L'état de la base de données est abstrait par les
concepts DataSet et DataTable, tous les deux représentés par les
interfaces
``PHPUnit_Extensions_Database_DataSet_IDataSet`` et
``PHPUnit_Extensions_Database_DataSet_IDataTable``.
La prochaine section décrira en détail comment ces concepts fonctionnent
et quels sont les avantages à les utiliser lors des tests de base de données.

Pour l'implémentation, nous avons seulement besoin de savoir que la méthode
``getDataSet()`` est appelée une fois dans
``setUp()`` pour récupérer l'ensemble de données de la fixture
et l'insérer dans la base de données. Dans l'exemple, nous utilisons une méthode
fabrique ``createFlatXMLDataSet($filename)`` qui
représente un ensemble de données à l'aide d'une représentation XML.

.. _database.what-about-the-database-schema-ddl:

Qu'en est-il du schéma de base de données (DDL)?
================================================

PHPUnit suppose que le schéma de base de données avec toutes ses tables,
ses triggers, séquences et vues est créé avant qu'un test soit exécuté.
Cela signifie que vous, en tant que développeur, devez vous assurer que la base
de données est correctement configurée avant de lancer la suite de tests.

Il y a plusieurs moyens pour satisfaire cette condition préalable
au test de base de données.

#.

   Si vous utilisez une base de données persistante (pas Sqlite en mémoire) vous pouvez
   facilement configure la base de données avec des outils tels que phpMyAdmin pour
   MySQL et réutiliser la base de données pour chaque exécution de test.

#.

   Si vous utilisez des bibliothèques comme
   `Doctrine 2 <http://www.doctrine-project.org>`_ ou
   `Propel <http://www.propelorm.org/>`_
   vous pouvez utiliser leurs APIs pour créer le schéma de base de données dont
   vous avez besoin une fois avant de lancer vos tests. Vous pouvez utiliser les possibilités apportées par
   `l'amorce et la configuration de PHPUnit <http://www.phpunit.de/manual/current/en/textui.html>`_
   pour exécuter ce code à chaque fois que vos tests sont exécutés.

.. _database.tip-use-your-own-abstract-database-testcase:

Astuce: utilisez votre propre cas de tests abstrait de base de données
======================================================================

En partant des exemples d'implémentation précédents, vous pouvez facilement voir que la méthode
``getConnection()`` est plutôt statique et peut être
réutilisée dans différents cas de test de base de données. Additionnellement
pour conserver de bonnes performances pour vos tests et maintenir la charge de la
base de données basse vous pouvez refactoriser un peu le code pour obtenir
un cas de test abstrait générique pour votre application, qui vous permette encore
d'indiquer des données de fixture différentes pour chaque cas de test :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class MyApp_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit_Extensions_Database_DB_IDatabaseConnection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }
    ?>

Mais la connexion à la base de données reste codée en dur dans la
connexion PDO. PHPUnit possède une autre fonctionnalité formidable
qui peut rendre ce cas de test encore plus générique. Si vous utilisez
`la configuration XML <appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables>`_,
vous pouvez rendre la connexion à la base de données configurable pour chaque exécution de test.
Créons d'abord un fichier “phpunit.xml“ dans le répertoire tests/
de l'application qui ressemble à ceci :

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

Nous pouvons maintenant modifier notre cas de test pour qu'il ressemble à ça :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class Generic_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit_Extensions_Database_DB_IDatabaseConnection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }
    ?>

Nous pouvons maintenant lancer la suite de tests de la base de données en utilisant différentes
configurations depuis l'interface en ligne de commandes:

.. code-block:: bash

    $ user@desktop> phpunit --configuration developer-a.xml MyTests/
    $ user@desktop> phpunit --configuration developer-b.xml MyTests/

La possibilité de lancer facilement des tests de base de données sur différentes
bases de données cibles est très important si vous développez sur une machine
de développement. Si plusieurs développeurs exécutent les tests de base de données
sur la même connexion de base de données, vous pouvez facilement faire l'expérience
d'échec de tests du fait des concurrences d'accès.

.. _database.understanding-datasets-and-datatables:

Comprendre DataSets et DataTables
#################################

Un concept centre de l'extension de base de données PHPUnit sont les
DataSets et les DataTables. Vous devez comprendre ce simple concept
pour maîtriser les tests de bases de données avec PHPUnit. Les DataSets
et les DataTables constituent une couche d'abstraction sur les tables,
les lignes et les colonnes de la base de données. Une simple API cache le contenu
de la base de données sous-jacente dans une structure objet, qui peut également
être implémentée par d'autres sources qui ne sont pas des bases de données.

Cette abstraction est nécessaire pour comparer le contenu constaté
d'une base de données avec le contenu attendu. Les attentes peuvent
être représentées dans des fichiers XML, YAML ou CSV ou des tableaux PHP
par exemple. Les interfaces DataSets et DataTables permettent de comparer
ces sources conceptuellement différentes en émulant un stockage en base
de données relationnelle dans une approche sémantiquement similaire.

Un processus pour des assertions de base de données dans vos tests
se limitera alors à trois étapes simples :

-

  Indiquer une ou plusieurs tables dans votre base de données via leurs
  noms de table (ensemble de données constatées)

-

  Indiquez l'ensemble de données attendu dans votre format préféré (YAML, XML,
  ..)

-

  Affirmez que les représentations des deux ensembles de données sont égaux.

Les assertions ne constituent pas le seul cas d'utilisation des DataSets
et DataTables dans l'extension de base de données PHPUnit. Comme illustré
dans la section précédente, ils décrivent également le contenu initial de la
base de données. Vous êtes obligés de définir un ensemble de données fixture
avec le cas de test Database, qui est ensuite utilisé pour :

-

  Supprimer toutes les lignes des tables indiquées dans le DataSet.

-

  Ecrire toutes les lignes dans les tables de données dans la base de données.

.. _database.available-implementations:

Implémentations disponibles
===========================

Il existe trois types différents de datasets/datatables:

-

  DataSets et DataTables basés sur des fichiers

-

  DataSets et DataTables basés sur des requêtes

-

  DataSets et DataTables de filtre et de combinaison

les datasets et les tables basés sur des fichiers sont généralement
utilisés pour la fixture initiale et pour décrire l'état attendu d'une base de données.

.. _database.flat-xml-dataset:

DataSet en XML à plat
---------------------

Le dataset le plus commun est appelé XML à plat (flat XML). C'est un format
xml très simple dans lequel une balise à l'intérieur d'un noeud racine
``<dataset>`` représente exactement une ligne de la base
de données. Les noms des balises sont ceux des tables dans lesquelles insérer les
lignes et un attribut représente la colonne. Un exemple pour une simple application
de livre d'or pourrait ressembler à ceci :

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

C'est à l'évidence facile à écrire. Ici,
``<guestbook>`` est le nom de la table dans laquelle les deux
lignes sont insérées, chacune avec quatre colonnes “id“,
“content“, “user“ et
“created“ et leurs valeurs respectives.

Cependant, cette simplicité a un coût.

Avec l'exemple précédent, difficile de voir comment nous devons indiquer
une table vide. Vous pouvez insérer une balise avec aucun attribut
contenant le nom de la table vide. Un fichier XML à plat pour une table
livre_d_or pourrait alors ressembler à ceci:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

La gestion des valeurs NULL avec le dataset en XML à plat est fastidieuse.
Une valeur NULL est différente d'une chaîne vide dans la plupart des bases
de données (Oracle étant une exception), quelque chose qu'il est difficile
de décrire dans le format XML à plat. Vous pouvez représenter une valeur NULL
en omettant d'attribut indiquant la ligne. Si votre livre d'or autorise les entrées
anonymes représentées par une valeur NULL dans la colonne utilisateur, un état
hypothétique de la table guestbook pourrait r
essembler à ceci:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" created="2010-04-26 12:14:20" />
    </dataset>

Dans ce cas, la seconde entrée est postée anonymement. Cependant,
ceci conduit à un sérieux problème pour la reconnaissance de la colonne.
Lors des assertions d'égalité de datasets, chaque dataset doit indiquer
quelle colonne une table contient. Si un attribut est NULL pour toutes les
lignes de la data-table, comment l'extension de base de données sait que la colonne
doit faire partie de la table ?

Le dataset en XML à plat fait maintenant une hypothèse cruciale en
décrétant que les attributs de la première ligne définie pour une table
définissent les colonnes de cette table. Dans l'exemple précédent, ceci signifierait que
“id“, “content“, “user“ et
“created“ sont les colonnes de la table guestbook. Pour la
seconde ligne dans laquelle “user“ n'est pas défini, un NULL
sera inséré dans la base de données.

Quand la première entrée du livre d'or est supprimée du dataset,
seuls “id“, “content“ et
“created“ seront des colonnes de la table guestbook,
puisque “user“ n'est pas indiqué.

Pour utiliser efficacement le dataset au format XML à plat quand des valeurs
NULL sont pertinentes, la première ligne de chaque table ne doit contenir aucune
valeur NULL, seules les lignes suivantes pouvant omettre des attributs.
Ceci peut s'avérer délicat, puisque l'ordre des lignes est un élément
pertinent pour les assertions de base de données.

A l'inverse, si vous n'indiquez qu'un sous-élément des colonnes de la table
dans le dataset au format XML à plat, toutes les valeurs omises sont positionnées
à leurs valeurs par défaut. Ceci provoquera des erreurs si l'une des valeurs omises
est définie par “NOT NULL DEFAULT NULL“.

En conclusion, je ne peux que vous conseiller de n'utiliser les datasets
au format XML à plat que si vous n'avez pas besoin des valeurs NULL.

Vous pouvez créer une instance de dataset au format XML à plat
dans votre cas de test de base de données en appelant la méthode
``createFlatXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }
    ?>

.. _database.xml-dataset:

DataSet XML
-----------

Il existe un autre dataset XML davantage structuré, qui est un peu plus
verbeux à écrire mais qui évite les problèmes de NULL du dataset au
format XML à plat. Dans le noeud racine ``<dataset>`` vous
pouvez indiquer les balises ``<table>``,
``<column>``, ``<row>``,
``<value>`` et
``<null />``. Un dataset équivalent à celui
défini précédemment pour le livre d'or en format XML à plat ressemble à :

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Hello buddy!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>I like it!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

Tout ``<table>`` défini possède un nom et nécessite
la définition de toutes les colonnes avec leurs noms. Il peut contenir zéro ou tout
nombre positif d'éléments ``<row>`` imbriqués.
Ne définir aucun élément ``<row>`` signifie que la table est vide.
Les balises ``<value>`` et
``<null />`` doivent être indiquées dans l'ordre des éléments
``<column>``précédemment donnés.
La balise ``<null />`` signifie évidemment que la
valeur est NULL.

Vous pouvez créer une instance de dataset xml dans votre
cas de test de base de données en appelant la méthode
``createXmlDataSet($filename)`` :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }
    ?>

.. _database.mysql-xml-dataset:

DataSet XML MySQL
-----------------

Ce nouveau format XML est spécifique au
`serveur de bases de données MySQL <http://www.mysql.com>`_.
Sa gestion a été ajoutée dans PHPUnit 3.5. Les fichiers écrits ce format peuvent
être générés avec l'utilitaire
```mysqldump`` <http://dev.mysql.com/doc/refman/5.0/en/mysqldump.html>`_.
Contrairement aux datasets CSV, que ``mysqldump``
gère également, un unique fichier de ce format XML peut contenir des données
pour de multiples tables. Vous pouvez créer un fichier dans ce format en
invoquant ``mysqldump`` de cette façon :

.. code-block:: bash

    $ mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

Ce fichier peut être utilisé dans votre case de test de base de données en appelant
la méthode ``createMySQLXMLDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }
    ?>

.. _database.yaml-dataset:

DataSet YAML
------------

Alternativement, vous pouvez utiliser un dataset YAML pour l'exemple du livre d'or:

.. code-block:: bash

    guestbook:
      -
        id: 1
        content: "Hello buddy!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "I like it!"
        user:
        created: 2010-04-26 12:14:20

C'est simple, pratique ET ça règle le problème de NULL que pose le dataset
équivalent au format XML à plat. Un NULL en YAML s'exprime simplement
en donnant le nom de la colonne sans indiquer de valeur. Une chaîne vide est indiquée par
``column1: ""``.

Le dataset YAML ne possède pas actuellement de méthode de fabrique
pour le cas de tests de base de données, si bien que vous devez l'instancier manuellement :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\YamlDataSet;

    class YamlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new YamlDataSet(dirname(__FILE__)."/_files/guestbook.yml");
        }
    }
    ?>

.. _database.csv-dataset:

DataSet CSV
-----------

Un autre dataset au format fichier est basé sur les fichiers CSV. Chaque table
du dataset est représenté par un fichier CSV. Pour notre exemple de livre d'or,
nous pourrions définir un fichier guestbook-table.csv:

.. code-block:: bash

    id,content,user,created
    1,"Hello buddy!","joe","2010-04-24 17:15:23"
    2,"I like it!","nancy","2010-04-26 12:14:20"

Bien que ce soit très pratique à éditer avec Excel ou OpenOffice,
vous ne pouvez pas indiquer de valeurs NULL avec le dataset CSV. Une
colonne vide conduira à ce que la valeur vide par défaut de la base de
données soit insérée dans la colonne.

Vous pouvez créer un dataset CSV en appelant :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\CsvDataSet;

    class CsvGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            $dataSet = new CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }
    ?>

.. _database.array-dataset:

DataSet tableau
---------------

Il n'existe pas (encore) de DataSet basé sur les tableaux dans
l'extension base de données de PHPUnit, mais vous pouvez implémenter
facilement la vôtre. Notre exemple du Livre d'or devrait ressembler à :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ArrayGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(
                [
                    'guestbook' => [
                        [
                            'id' => 1,
                            'content' => 'Hello buddy!',
                            'user' => 'joe',
                            'created' => '2010-04-24 17:15:23'
                        ],
                        [
                            'id' => 2,
                            'content' => 'I like it!',
                            'user' => null,
                            'created' => '2010-04-26 12:14:20'
                        ],
                    ],
                ]
            );
        }
    }
    ?>

Un DataSet PHP possède des avantages évidents sur les autres datasets utilisant des
fichiers :

-

  Les tableaux PHP peuvent évidemment gérer les valeurs ``NULL``.

-

  Vous n'avez pas besoin de fichiers additionnels pour les assertions et vous pouvez
  les renseigner directement dans les cas de test.

Pour que ce dataset ressemble aux DataSets au format XML à plat, CSV et YAML, les clefs
de la première ligne spécifiée définissent les noms de colonne de la table, dans le cas
précédent, ce serait “id“,
“content“, “user“ and
“created“.

L'implémentation de ce DataSet tableau est simple et
évidente:

.. code-block:: php

    <?php
    class MyApp_DbUnit_ArrayDataSet extends PHPUnit_Extensions_Database_DataSet_AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = [];

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data AS $tableName => $rows) {
                $columns = [];
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new PHPUnit_Extensions_Database_DataSet_DefaultTableMetaData($tableName, $columns);
                $table = new PHPUnit_Extensions_Database_DataSet_DefaultTable($metaData);

                foreach ($rows AS $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = false)
        {
            return new PHPUnit_Extensions_Database_DataSet_DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName is not a table in the current database.");
            }

            return $this->tables[$tableName];
        }
    }
    ?>

.. _database.query-sql-dataset:

Query (SQL) DataSet
-------------------

Pour les assertions de base de données, vous n'avez pas seulement besoin
de datasets basés sur des fichiers mais aussi de Datasets basé sur des requêtes/du SQL
qui contiennent le contenu constaté de la base de données. C'est là que le DataSet Query s'illustre :

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');
    ?>

Ajouter une table juste par son nom est un moyen implicite de définir
la table de données avec la requête suivante :

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');
    ?>

Vous pouvez utiliser ceci en indiquant des requêtes arbitraires pour
vos tables, par exemple en restreignant les lignes, les colonnes ou en
ajoutant des clauses ``ORDER BY``:

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');
    ?>

La section relative aux assertions de base de données montrera plus en détails comment
utiliser le Query DataSet.

.. _database.database-db-dataset:

Dataset (DB) de base de données
-------------------------------

En accédant à la connexion de test, vous pouvez créer automatiquement
un DataSet constitué de toutes les tables et de leur contenu de la base
de données indiquée comme second paramètre de la méthode fabrique de
connexion.

Vous pouvez, soit créer un dataset pour la base de données complète
comme montré dans la méthode ``testGuestbook()``, soit le restreindre
à un ensemble de noms de tables avec une liste blanche comme montré dans
la méthode ``testFilteredGuestbook()``.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MySqlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }
    ?>

.. _database.replacement-dataset:

DataSet de remplacement
-----------------------

J'ai évoqué les problèmes de NULL avec les DataSet au format XML à
plat et CSV, mais il y existe un contournement légèrement compliqué
pour que ces deux types de datasets fonctionnent avec NULLs.

Le DataSet de remplacement est un décorateur pour un dataset existant
et vous permet de remplacer des valeurs dans toute colonne du dataset
par une autre valeur de remplacement. Pour que notre exemple de livre d'or
fonctionne avec des valeurs NULL nous indiquons le fichier comme ceci:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Nous encapsulons le DataSet au format XML à plat dans le DataSet de remplacement :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ReplacementTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit_Extensions_Database_DataSet_ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }
    ?>

.. _database.dataset-filter:

Filtre de DataSet
-----------------

Si vous avez un fichier de fixture conséquent vous pouvez utiliser
le filtre de DataSet pour des listes blanches ou noires des tables
et des colonnes qui peuvent être contenues dans un sous-dataset. C'est
particulièrement commode en combinaison avec le DataSet de base de données pour filtrer les colonnes des datasets.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class DataSetFilterTest extends TestCase
    {
        use TestCaseTrait;

        public function testIncludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(['guestbook']);
            $filterDataSet->setIncludeColumnsForTable('guestbook', ['id', 'content']);
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(['foo', 'bar', 'baz']); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', ['user', 'created']);
            // ..
        }
    }
    ?>

    *NOTE* Vous ne pouvez pas utiliser en même temps le filtrage de colonne d'inclusion
    et d'exclusion sur la même table, seulement sur des tables différentes. De plus, il est
    seulement possible d'appliquer soit une liste blanche, soit une liste
    noire aux tables, mais pas les deux à la fois.

.. _database.composite-dataset:

DataSet composite
-----------------

Le DataSet composite est très utile pour agréger plusieurs
datasets déjà existants dans un unique dataset. Quand plusieurs
datasets contiennent la même table, les lignes sont ajoutées
dans l'ordre indiqué. Par exemple, si nous avons deux datasets
*fixture1.xml* :

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

et *fixture2.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

En utiliser le DataSet composite, nous pouvons agréger les deux fichiers de fixture:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class CompositeTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit_Extensions_Database_DataSet_CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }
    ?>

.. _database.beware-of-foreign-keys:

Attention aux clefs étrangères
==============================

Lors du SetUp de la fixture l'extension de base de données de PHPUnit
insère les lignes dans la base de données dans l'ordre où elles sont
indiquées dans votre fixture. Si votre schéma de base de données
utilise des clefs étrangères, ceci signifie que vous devez indiquer
les tables dans un ordre qui ne provoquera pas une violation de contrainte pour ces clefs étrangères.

.. _database.implementing-your-own-datasetsdatatables:

Implémenter vos propres DataSets/DataTables
===========================================

Pour comprendre le fonctionnement interne des DataSets et des DataTables
jetons un oeil sur l'interface d'un DataSet. Vous pouvez sauter cette partie
si vous ne projetez pas d'implémenter votre propre DataSet ou DataTable.

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DataSet_IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_IDataSet $other);

        public function getReverseIterator();
    }
    ?>

L'interface publique est utilisée en interne par l'assertion
``assertDataSetsEqual()`` du cas de test de base de données
pour contrôler la qualité du dataset. De l'interface
``IteratorAggregate`` le IDataSet
hérite la méthode ``getIterator()`` pour parcourir toutes
les tables du dataset. La méthode additionnelle d'itérateur inverse est
nécessaire pour réussir à tronquer les tables dans l'ordre inverse à celui indiqué pour satisfaire
les contraintes de clés étrangères.

En fonction de l'implémentation, différentes approches sont prises
pour ajouter des instances de table dans un dataset. Par exemple,
les tables sont ajoutées de façon interne lors de la construction
depuis le fichier source dans tous les datasets basés sur les fichiers comme
``YamlDataSet``, ``XmlDataSet`` ou ``FlatXmlDataSet``.

Une table est également représentée par l'interface suivante :

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DataSet_ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_ITable $other);
    }
    ?>

Mise à part la méthode ``getTableMetaData()``, ça parle
plutôt de soi-même. Les méthodes utilisées sont toutes nécessaires pour
les différentes assertions de l'extension Base de données expliquées
dans le chapitre suivant. La méthode
``getTableMetaData()`` doit retourner
une implémentation de l'interface
``PHPUnit_Extensions_Database_DataSet_ITableMetaData``
qui décrit la structure de la table. Elle contient des
informations sur:

-

  Le nom de la table

-

  Un tableau des noms de colonne de la table, classé par leur ordre
  d'apparition dans l'ensemble résultat.

-

  Un tableau des colonnes clefs primaires.

Cette interface possède également une assertion qui contrôle si
deux instances des méta données des tables sont égales et qui sera
utilisée par l'assertion d'égalité d'ensemble de données.

.. _database.the-connection-api:

Utiliser l'API de connexion à la base de données
################################################

Il y a trois méthodes intéressantes dans l'interface de connexion
qui doit être retournée par la méthode
``getConnection()`` du cas de test de base de données :

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DB_IDatabaseConnection
    {
        public function createDataSet(Array $tableNames = NULL);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = NULL);

        // ...
    }
    ?>

#.

   La méthode ``createDataSet()`` crée un DataSet de base de données
   (DB) comme décrit dans la section relative aux implémentations de DataSet.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSet()
           {
               $tableNames = ['guestbook'];
               $dataSet = $this->getConnection()->createDataSet();
           }
       }
       ?>

#.

   La méthode ``createQueryTable()`` peut être utilisée pour
   créer des instances d'une QueryTable, en lui passant un nom de résultat et
   une requête SQL. C'est une méthode pratique quand elle est associée à des
   assertions résultats/table comme cela sera illustré dans la prochaine section
   relative à l'API des assertions de base de données.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateQueryTable()
           {
               $tableNames = ['guestbook'];
               $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
           }
       }
       ?>

#.

   La méthode ``getRowCount()`` est un moyen pratique d'accéder
   au nombre de lignes d'une table, éventuellement filtrées par une
   clause where supplémentaire. Ceci peut être utilisé pour une simple
   assertion d'égalité :

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testGetRowCount()
           {
               $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'));
           }
       }
       ?>

.. _database.database-assertions-api:

API d'assertion de base de données
##################################

En tant qu'outil de test, l'extension base de données fournit
certainement des assertions que vous pouvez utiliser pour vérifier
l'état actuel de la base de données, des tables et du nombre de lignes
des tables. Cette section décrit ces fonctionnalités en détail :

.. _database.asserting-the-row-count-of-a-table:

Faire une assertion sur le nombre de lignes d'une table
=======================================================

Il est souvent très utile de vérifier si une table contient un nombre
déterminé de lignes. Vous pouvez facilement réaliser cela sans code
de liaison supplémentaire en utilisant l'API de connexion. Disons que
nous voulons contrôler qu'après une insertion d'une ligne dans notre
livre d'or, nous n'avons plus seulement nos deux entrées initiales
qui nous ont accompagnées dans tous les exemples précédents, mais aussi une troisième :

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertEquals(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }
    ?>

.. _database.asserting-the-state-of-a-table:

Faire une assertion sur l'état d'une table
==========================================

L'assertion précédente est utile, mais nous voudrons certainement tester
le contenu présent de la table pour vérifier que toutes les valeurs ont
été écrites dans les bonnes colonnes. Ceci peut être réalisé avec une assertion
de table.

Pour cela, nous devons définir une instance de Query Table qui tire
son contenu d'un nom de table et d'une requête SQL et le compare
à un DataSet basé sur un fichier/tableau.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

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

Maintenant, nous devons écrire le fichier XML à plat *expectedBook.xml*
pour cette assertion :

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Hello world!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

Cette assertion ne réussira que si elle est lancée très exactement le
*2010–05–01 21:47:08*. Les dates posent un problème spécial
pour le test de base de données et nous pouvons contourner l'échec
en omettant la colonne “created“ de
l'assertion.

Le fichier au format XML à plat adapté *expectedBook.xml*
devra probablement ressembler à ce qui suit pour que
l'assertion réussisse :

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" />
        <guestbook id="2" content="I like it!" user="nancy" />
        <guestbook id="3" content="Hello world!" user="suzy" />
    </dataset>

Nous devons corriger l'appel à Query Table:

.. code-block:: php

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );
    ?>

.. _database.asserting-the-result-of-a-query:

Faire une assertion sur le résultat d'une requête
=================================================

Vous pouvez également faire une assertion sur le résultat de requêtes
complexes avec l'approche Query Table, simplement en indiquant le nom
d'un résultat avec une requête et en le comparant avec un ensemble de données:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ComplexQueryTest extends TestCase
    {
        use TestCaseTrait;

        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _database.asserting-the-state-of-multiple-tables:

Faire une assertion sur l'état de plusieurs tables
==================================================

Evidemment, vous pouvez faire une assertion sur l'état de plusieurs tables à la fois
et comparer un ensemble de données obtenu par une requête avec un ensemble de données
basé sur un fichier. Il y a deux façons différentes de faire des assertions de DataSet.

#.

   Vous pouvez utiliser le Database (DB) Dataset à partir de la
   connexion et le comparer au DataSet basé sur un fichier.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSetAssertion()
           {
               $dataSet = $this->getConnection()->createDataSet(['guestbook']);
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

#.

   Vous pouvez construire vous-même le DataSet:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testManualDataSetAssertion()
           {
               $dataSet = new PHPUnit_Extensions_Database_DataSet_QueryDataSet();
               $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // additional tables
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

.. _database.frequently-asked-questions:

Foire aux questions
###################

.. _database.will-phpunit-re-create-the-database-schema-for-each-test:

PHPUnit va-t'il (re-)créer le schéma de base de données pour chaque test ?
==========================================================================

Non, PHPUnit exige que tous les objets de base de données soit disponible quand
la suite démarre. La base de données, les tables, les séquences, les triggers et les
vues doivent être créés avant que vous exécutiez la suite de tests.

`Doctrine 2 <http://www.doctrine-project.org>`_ ou
`eZ Components <http://www.ezcomponents.org>`_ possèdent
des outils puissants qui vous permettent de créer le schéma de base de données
à partir de structures de données définies préalablement, cependant, ceux-ci
doivent être reliés à l'extension PHPUnit pour permettre la recréation automatique
de la base de données avant que la suite de tests complète ne soit exécutée.

Puisque chaque test nettoie complètement la base de données, vous n'avez
même pas obligation de re-créer la base de donnée pour chaque exécution des tests.
Une base de données disponible de façon permanente fonctionne parfaitement.

.. _database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work:

Suis-je obligé d'utiliser PDO dans mon application pour que l'extension de base de données fonctionne ?
=======================================================================================================

Non, PDO n'est nécessaire que pour le nettoyage et la configuration de la fixture et
pour les assertions. Vous pouvez utiliser n'importe laquelle des abstractions de base de
données que vous voulez dans votre propre code.

.. _database.what-can-i-do-when-i-get-a-too-much-connections-error:

Que puis-je faire quand j'obtiens une erreur “Too much Connections (Trop de connexions)“ ?
==============================================================================================

Si vous ne mettez pas en cache l'instance PDO qui est créée dans
la méthode ``getConnection()`` du cas de test
le nombre de connexions à la base de données est augmenté d'une unité ou plus
pour chaque test de base de données. Avec la configuration par défaut, MySql
n'autorise qu'un maximum de 100 connexions concurrentes. Les autres moteurs
de bases de données possèdent également des limites du nombre maximum de connexions.

La sous-section
“Utilisez votre propre cas de test de base de données abstrait“ illustre comment
vous pouvez empêcher cette erreur de survenir en utilisant une unique instance de PDO en cache
dans tous vos tests.

.. _database.how-to-handle-null-with-flat-xml-csv-datasets:

Comment gérer les valeurs NULL avec les DataSets au format XML à plat / CSV ?
=============================================================================

Ne le fait pas. Pour cela, vous devez utiliser des
DataSets XML ou YAML.


