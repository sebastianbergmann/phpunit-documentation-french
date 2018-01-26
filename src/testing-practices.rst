

.. _testing-practices:

=================
Pratiques de test
=================

    *Erich Gamma*:

    Vous pouvez toujours écrire davantage de tests. Cependant, vous vous rendrez rapidement compte que
    seule une fraction des tests auxquels vous pensez est réellement utile. Ce
    que vous voulez, c'est écrire des tests qui échouent même si vous pensez qu'ils devraient
    fonctionner, ou des tests qui réussissent même si vous pensez qu'ils devraient échouer.
    Une autre façon d'envisager cela consiste à le faire en terme de coûts/bénéfices. Vous voulez écrire
    des tests qui vous paient en retour avec des informations.

.. _testing-practices.during-development:

Pendant le développement
########################

Lorsque vous avez besoin de modifier la structure interne d'un logiciel sur
lequel vous êtes en train de travailler pour la rendre plus facile à comprendre et moins chère à modifier
sans modifier son comportement visible, une suite de tests est inestimable pour
appliquer de façon sûre ces `refactorings <http://martinfowler.com/bliki/DefinitionOfRefactoring.html>`_
comme on les appelle. Sans cela, vous ne pourrez pas vous détecter
que vous avez cassé le système en réalisant la restructuration.

Les conditions suivantes vous aideront à améliorer le code et la conception
de votre projet, tant que vous utiliserez des tests unitaires pour vérifier
que les étapes de transformation dues au refactoring préservent bien le
comportement et n'introduisent pas d'erreurs :

#.

   Tous les tests unitaires fonctionnent correctement.

#.

   Le code transmet des principes de conception.

#.

   Le code ne contient pas de redondances.

#.

   Le code contient le nombre minimal de classes et de méthodes.

Quand vous avez besoin d'ajouter de nouvelles fonctionnalités au système,
écrivez d'abord les tests. Puis, vous aurez terminé quand les tests
s'exécuteront. Cette pratique sera discutée en détail dans le prochain chapitre.

.. _testing-practices.during-debugging:

Pendant le débogage
###################

Quand vous recevez un rapport de bug, vous pourriez être tenté de
le corriger aussi vite que possible. L'expérience prouve que cette
impulsion ne vous rendra pas service ; il est probable que ce défaut en
provoque un autre.

Vous pouvez maîtriser cette impulsion en suivant ce qui suit :

#.

   Vérifiez que vous pouvez reproduire le défaut.

#.

   Trouvez la démonstration la plus ciblée possible du défaut dans le code.
   Par exemple, si un nombre s'affiche incorrectement dans une sortie écran,
   trouvez l'objet qui calcule ce nombre.

#.

   Ecrivez un test automatisé qui échoue maintenant mais qui réussira quand
   le défaut sera corrigé.

#.

   Corrigez le défaut.

Trouver le moyen fiable le plus ciblé pour reproduire le défaut vous
offre l'opportunité de vraiment en examiner la cause. Le test que vous
écrivez va améliorer les chances que lorsque vous corrigerez le défaut,
vous le fassiez vraiment du fait que le nouveau test réduit la probabilité
de défaire cette correction lors de futures modifications du code. Tous les tests
écrits avant réduisent la probabilité de causer par inadvertance un problème différent.

    *Benjamin Smedberg*:

    Le test unitaire offre de nombreux avantages :

    -

      Tester conforte les auteurs de code et les relecteurs dans le fait que les correctifs produisent des résultats corrects..

    -

      Créer des cas de tests est un bon moyen de conduire les développeurs à découvrir des cas limites.

    -

      Tester fournit un bon moyen de capturer les régressions rapidement et de s'assurer qu'elles ne seront pas répétées deux fois.

    -

      Les tests unitaires fournissent des exemples fonctionnels de la façon d'utiliser une API et peuvent aider significativement à l'effort de documentation.

    Globalement, le test unitaire intégré minimise les coûts et les risques
    de toute modification individuelle. Il permettra au projet de mener \[...]
    des améliorations majeures d'architecture \[...] rapidement et avec confiance.


