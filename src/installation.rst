

.. _installation:

=================
Installer PHPUnit
=================

.. _installation.requirements:

Pré-requis
##########

PHPUnit |version| nécessite PHP 7; utiliser la dernière version de PHP est fortement
recommandé.

PHPUnit nécessite les extensions `dom <http://php.net/manual/en/dom.setup.php>`_ et `json <http://php.net/manual/en/json.installation.php>`_
qui sont traditionnellement activées par défaut.

PHPUnit nécessite aussi les extensions
`pcre <http://php.net/manual/en/pcre.installation.php>`_,
`reflection <http://php.net/manual/en/reflection.installation.php>`_,
et `spl <http://php.net/manual/en/spl.installation.php>`_.
Ces extensions standard sont activées par défaut et ne peuvent être désactivées
sans patcher le système de construction de PHP ou/et les sources en C.

La fonctionnalité de couverture de code nécessite l'extension
`Xdebug <http://xdebug.org/>`_ (2.5.0 ou ultérieur) et
`tokenizer <http://php.net/manual/en/tokenizer.installation.php>`_.
La génération des rapports XML nécessite l'extension
`xmlwriter <http://php.net/manual/en/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

La manière la plus simple d'obtenir PHPUnit est de télécharger `l'Archive PHP (PHAR) <http://php.net/phar>`_ qui contient toutes les
dépendances requises (ainsi que certaines optionnelles) de PHPUnit archivées en un seul
fichier.

L'extension `phar <http://php.net/manual/en/phar.installation.php>`_
est requise pour utiliser les archives PHP (PHAR).

Si l'extension `Suhosin <http://suhosin.org/>`_ est
activée, vous devez autoriser l'exécution des PHAR dans votre
``php.ini``:

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

Pour installer le PHAR de manière globale :

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  chmod +x phpunit-|version|.phar
    $  sudo mv phpunit-|version|.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

Vous pouvez également utiliser directement le fichier PHAR téléchargé:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

L'installation globale du PHAR implique la même procédure que l'installation
`manuelle de Composer sous Windows <https://getcomposer.org/doc/00-intro.md#installation-windows>`_:

#.

   Créer un répertoire pour les binaires PHP; ex. : :file:`C:\\bin`

#.

   Ajouter ;C:\bin à votre variable d'environnement
   ``PATH``
   (`related help <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_)

#.

   Télécharger `<https://phar.phpunit.de/phpunit-|version|.phar>`_ et
   sauvegarder le fichier sous :file:`C:\\bin\\phpunit.phar`

#.

   Ouvrir une ligne de commande (par exemple,
   appuyez :kbd:`Windows`:kbd:`R`
   » et tapez cmd
   » :kbd:`ENTER`)

#.

   Créer un script batch (dans
   :file:`C:\\bin\\phpunit.cmd`):

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   Ouvrez une nouvelle ligne de commande et confirmez que vous pouvez exécuter PHPUnit à partir de n'importe
   quel chemin:

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

Pour les environments shell Cygwin et/ou MingW32 (ex: TortoiseGit), vous
passer l'étape 5. ci-dessus, il suffit de sauvegarder le fichier
:file:`phpunit` (sans l'extension :file:`.phar`),
et de le rendre exécutable via
chmod 775 phpunit.

.. _installation.phar.verification:

Vérification des versions PHAR de PHPUnit
=========================================

Toutes les versions officielles de code distribuées par le projet PHPUnit sont
signées par le responsable de publication de la version. Les signatures PGP et les hachages
SHA1 sont disponibles pour vérification sur `phar.phpunit.de <https://phar.phpunit.de/>`_.

L'exemple suivant détaille le fonctionnement de la vérification de version. Nous commençons
par télécharger :file:`phpunit.phar` ainsi que sa
signature PGP détachée :file:`phpunit.phar.asc`:

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

Nous voulons vérifier l'archive PHP Phar de PHPUnit (:file:`phpunit.phar`)
par rapport à sa signature détachée (:file:`phpunit.phar.asc`):

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

Nous n'avons pas la clé publique du responsable de la publication (``6372C20A``)
dans notre système local. Afin de procéder à la vérification, nous devons récupérer la clé
publique du gestionnaire de versions à partir d'un serveur de clés. Un de
ces serveurs est :file:`pgp.uni-mainz.de`. Les serveurs de clés publiques
sont liés entre eux, vous devriez donc pouvoir vous connecter à n'importe quel serveur de clés.

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

Nous avons maintenant reçu une clé publique pour une entité appelée "Sebastian
Bergmann <sb@sebastian-bergmann.de>". Cependant, nous n'avons aucun moyen
de vérifier que cette clé a été créée par la personne connue sous le nom de Sebastian
Bergmann. Mais, essayons de vérifier à nouveau la signature de la version délivrée.

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

À ce stade, la signature est bonne, mais nous ne faisons pas confiance à cette clé. Une
bonne signature signifie que le fichier n'a pas été falsifié. Cependant, en raison
de la nature de la cryptographie à clé publique, vous devez également
vérifier que la clé ``6372C20A`` a été créée par le vrai
Sebastian Bergmann.

Tout attaquant peut créer une clé publique et l'uploader sur les serveurs de clés
publiques. Ils peuvent ensuite créer une version malveillante signée par cette fausse
clé. Ensuite, si vous essayiez de vérifier la signature de cette version corrompue,
cela réussirait car la clé n'était pas la "vraie" clé. Par conséquent, vous
devez valider l'authenticité de cette clé. La validation de
l'authenticité d'une clé publique est toutefois hors de la portée de cette
documentation.

Il peut être prudent de créer un script shell pour gérer l'installation de PHPUnit
qui vérifie la signature de GnuPG avant d'exécuter votre suite de tests. Par
exemple:

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # Delete phpunit.phar after the tests are complete?
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # Let's clean them up, if they exist
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # Let's grab the latest release and its signature
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # Verify before running
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # Run the testing suite
        `$after_cmd`
        # Cleanup
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

Ajoutez simplement une dépendance (au développement) à
``phpunit/phpunit`` au fichier
``composer.json`` de votre projet si vous utilisez `Composer <https://getcomposer.org/>`_ pour gérer les
dépendances de votre projet :

.. code-block:: bash

    composer require --dev phpunit/phpunit ^|version|

.. _installation.optional-packages:

Paquets optionnels
##################

Les packages optionnels suivants sont disponibles:

``PHP_Invoker``

    Une classe d'utilitaire pour invoquer des appelables avec un délai d'expiration. Ce package est
    requis pour appliquer les délais d'attente de test en mode strict.

    Ce package est inclus dans la distribution PHAR de PHPUnit. Il peut
    être installé via Composer en utilisant la commande suivante :

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    Portage DbUnit pour PHP/PHPUnit pour prendre en charge le test d'interaction de base de données.

    Ce package n'est pas inclus dans la distribution PHAR de PHPUnit. Il peut
    être installé via Composer en utilisant la commande suivante :

    .. code-block:: bash

        composer require --dev phpunit/dbunit


