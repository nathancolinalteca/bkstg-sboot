Le starter kit et les bibliothèques Apave fournissent des implémentations par défaut permettant de résoudre simplement un certain nombre de problématiques.

La plupart des éléments sont divisés en deux :

- Les APIs: Jars minimalistes, avec seulement quelques annotations et interfaces.
- Les implémentation: Parfois très complexe, avec de nombreuses dépendances.

Les APIs sont mise à disposition par le pom racine java Apave (le parent du pom.xml de ce projet). Les annotations et interfaces sont donc à la disposition du développeur.

Par contre, les implémentations sont déclarées comme dépendances du projet générique "j2ee-impl", inclu dans le pom.xml de ce projet. Elle doivent être fournies dans le scope maven "runtime". Les jars (et leurs dépendances) seront donc bien inclu dans le war final, mais toutes les classes ne sont PAS disponible dans le classpath, évitant ainsi au développeur de faire des bêtises.

Chacune de ces problématique est traitée dans un document à part :

- [Injection de dépendances](normes/01-InjectionDependances.md)
- [Ecriture de services Rest](normes/02-ServicesRest.md)
- [Accès à une (ou plusieurs) base(s) de données](normes/03-Bdd.md)
- [Gestion de la configuration](normes/04-Config.md)
- [Logging](normes/05-Logging.md)
- [Parsing JSON](normes/06-ParsingJson.md)
- [Mapping d'objets](normes/07-Mapping.md)
- [Exécution de code en parallèle](normes/08-ExecutorService.md)
- [Tests unitaires](normes/09-TestsUnitaires.md)
- [Appeler un autre service Rest](normes-10-JaxRsClient.md)
