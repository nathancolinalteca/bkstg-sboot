Ce document décrit la structure des répertoires du projet, ainsi que tous les autres éléments qui ne font pas partie directement du code.

# Structure des répertoires

Le starter kit est défini sous la forme d'un projet maven. Tout le code est donc présent dans :

- Le répertoire src
- Le fichier pom.xml

Les développeurs pourront revoir cette organisation pour créer un projet multi module habituel.

Les autres répertoires correspondent à des éléments plus génériques :

- **config**: Contient la configuration pour le développement local, et pour XL Deploy.
    - **Local** contient la configuration qui sera utilisée par votre poste.
    - **xlDeploy** contient les deployables qui devront être déposés sur les environnements Apave. Les fichiers contiennent des placeholders XL qui seront remplacés par leurs valeurs (définies dans GestConf) lors du déploiement.
- **doc-files**: Contient les fichiers md qui complètent le fichier README.md situé à la racine.

A la racine, vous trouvez en plus quelques fichiers génériques :

- **Jenkinsfile**: Fichier pipeline Jenkins servant à déployer le projet sur les environnements Apave.
- **deployit-manifest.xml**: Fichier permettant de piloter le déploiement via XLDeploy

# Le pom.xml

A l'intérieur de ce fichier, on retrouve plusieurs éléments 

## Le pom parent

Tout projet Java chez Apave doit dépendre du pom parent suivant (la version pourra évoluer sans que ce document soit mis à jour...) :

    <parent>
      <groupId>com.apave</groupId>
      <artifactId>root-java</artifactId>
      <version>1.7.0</version>
    </parent>

Ce parent va vous configurer des plugins par défaut :

- Il va déclarer que vos ressources (src/main/resources) seront filtrées par maven, vous permettant de faire référence à des variables maven à l'intérieur.
- Il configure le compilateur en Java 8
- Et il configure l'analyse Sonar par défaut

Il va en plus vous apporter toutes les dépendances sur :

- Les APIs J2EE (et seulement les APIS !): CDI, JAX-RS et JPA
- Quelques autres APIs pratiques (à nouveau, seulement les APIs !): Deltaspike, Log4j2, swagger et javax.validation.
- Quelques bibliothèques utiles: Jackson, commons-io et commons-lang.
- Et des bibliotèques pour les tests : JUnit, Mockito et AssertJ

Il est fortement recommandé de ne pas surcharger ces dépendances dans le pom.xml du projet.

## Les dépendances

Elle sont de plusieurs types :

- Les dépendances vers les bibliothèques apave génériques (sauf une nommée "j2ee-impl"): Ces dépendances ajoutent des classes dans votre classpath pour vous aider à gérer divers aspects comme l'exécution de code en parallèle, l'accès à plusieurs bases de données, etc...
- La dépendance vers la bibliothèque Apave générique "j2ee-impl": Cette dépendance vous apporte toutes les **implémentations** J2EE. Votre code ne doit **pas** s'en servir. Pour cette raison, cette dépendance utilise le scope "runtime".
- Une dépendance vers la norme servlet 3.1: Elle vous sera fournie par Tomcat d'où son scope "provided"
- Des dépendances vers les pilotes JDBC dont vous avez besoin: A nouveau, ces classes ne doivent pas être manipulées, d'où leur scope "runtime".

Vous pourrez ensuite rajouter les dépendances nécessaires à votre projet. L'objectif est conserver un minimum de classes dans le classpath pendant le développement de manière à forcer les développeurs à rester dans la norme.

## Swagger

Le plugin maven swagger est configuré pour générer un 3 swaggers :

- Un swagger pour la version courante de vos services Rest.
- Un swagger pour la version précédente (deprecated) de vos services Rest.
- Et un swagger pour vos services Rest dédiés à votre front (si vous en avez un).

Ces fichiers seront généré dans le répertoire target/generated. Vous pourrez vous en service pour les importer dans un outil comme Postman, et vous générer une collection pour tester vos services.

## Cargo

Le plugin maven cargo est configuré pour lancer un serveur Tomcat 8.5 automatiquement sur le port 8080. En plus d'éviter au développeur d'en installer un en local, cela permet d'avoir une idée de la configuration attendue :

- La version exacte du Tomcat déployé en production
- Quelles propriétés système java doivent être ajoutées: Pour définir l'emplacement du fichier de conf log4j2 par exemple
- Quels fichiers doivent être déployés sur le système de fichier du Tomcat, et à quel endroit.

Le serveur est démarré en mode debug, sur le port 8001. Tant qu'un client de debug ne se connectera pas dessus, le serveur ne démarrera pas. Vous pouvez changer ce comportement en modifiant la ligne :

    <cargo.start.jvmargs>-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=8001</cargo.start.jvmargs>

Changez simplement le "suspend=y" en "suspend=n".

# Le Jenkinsfile

Ce fichier est exécuté par Jenkins à chaque modification. Il se charge de compiler l'application, de l'uploader sur le serveur Nexus, et de la déployer sur l'environnement de dev (uniquement s'il construit la branche develop).

A l'intérieur, on retrouve des références à :

- La liste des fichiers devant être déployés sur les environnements: On les appel des "déployables"
- Le trigramme de l'application
- L'identifiant XL de l'environnement de dev sur lequel déployer

# Le deployit-manifest.xml

Ce fichier indique à XL Deploy ce qu'il doit faire avec chacun des fichiers devant être déployé.

C'est ici que vous indiquerez ce qu'XL devra faire avec chaque fichier:

- Là où il devra les déposer
- Est ce qu'il doit remplacer les placeholders à l'intérieur
- etc...

Chaque fichier référencé dans le manifest doit avoir été remonté pendant le build par Jenkins (et donc avoir été déclaré comme tel dans le Jenkinsfile).