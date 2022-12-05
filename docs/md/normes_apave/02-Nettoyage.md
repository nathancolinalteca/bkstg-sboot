Avant de commencer, le développeur va devoir nettoyer le starter kit pour l'adapter à son projet.

# Supprimer la documentation du starter kit

Vous pouvez supprimer :

- le répertoire doc-files
- Et le fichier README.md

Il est bien sûr conseillé que vous écriviez les votre à la place.

Notez bien que cette documentation reste disponible dans le repository git du starter kit lui même. Vous pourrz donc continuer à y faire référence.

# Adaptations du pom.xml

L'architecte du projet doit fournir le nom du projet, qui sera repris comme artifactId. Dans cet exemple, nous considérerons que l'application s'appel "acme". 

## Les informations d'identification

- **ArtifactId**: Mettez le **nom de l'application** fournie par l'architecte (FIXME: Devrait être l'identifiant du composant non ?)
- **GroupId**: Il ne change pas. Il reste à "**com.apave**".
- **Version** : La première version sera la "**1.0.0-SNAPSHOT**".
- **Nom du projet**: Cette information est reprise à de nombreux endroits, dont Sonarqube. Il doit être de la forme "\[Trigramme Appli]\[Nom appli]\[Identifiant composant]\[Zone]" :
    - Le **trigramme** et le **nom de l'application** sont fournis par l'architecte
    - L'**id du composant** est de la forme (en minuscule) "trigramme-type-discriminant" :
        - **trigramme** : le même que celui de l'application, mais en minuscule
        - **type**: L'un des types existants :
            - be: Back end
            - fr: Front end
            - lib: Bibliothèque
            - dbb: Scripts SQL
            - plug: Plugin
            - etc...
        - **discriminant**: Un discriminant qui permet de nommer le composant.
    - **Zone**: Le code de la zone (ZSU, ZES, ZPA, ZNO ou APPLI)

## Les pilotes JDBC

Vous devez ensuite ajouter la dépendance vers le (ou les) pilote(s) JDBC :

Voici un exemple pour MySQL 5:

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.42</version>
      <scope>runtime</scope>
    </dependency>

Le pilote Oracle est à disposition dans le Nexus Apave :

    <dependency>
      <groupId>com.oracle.jdbc</groupId>
      <artifactId>ojdbc8</artifactId>
      <version>19.3.0.0</version>
      <scope>runtime</scope>
    </dependency>

Notez bien le scope runtime !

## La configuration cargo

Dans la configuration cargo :

- Changer le chemin vers le fichier de propriétés principal dans la configuration cargo.
- Si votre application n'utilise pas la FID, supprimer le déploiement du fichier fid-default-headers.properties

## La configuration swagger

Dans la déclaration des instances du plugin swagger :

- Changez le nom de l'application dans le tag &lt;title&gt;
- Ne conservez que les instances dont vous avez besoin. Par exempe, si votre application n'implémente qu'un service Rest destiné à être appelé par d'autres services (que vous n'avez pas de front), vous pouvez supprimer le swagger sur /rest/interne.

# Adaptation du web.xml

Dans le fichier src/main/webapp/WEB-INF/web.xml :

- Modifiez le contenu du tag &lt;display-name&gt; pour mettre le nom de votre application
- Nettoyez l'utilisation du filre CORS (inutile si vous n'avez pas de front, ou si vous passez par la FID v2)

# Fichiers de propriétés

## Propriétés générales

Renommez le fichier de propriété général (starter-kit.properties) situé dans config/Local et Config/xlDeploy.

Son nom doit être le même que l'artefact id du projet.

Renommez la classe com.apave.config.StarterKitPropertyFileConfig en AcmePropertyFileConfig, et adapter le nom du fichier à l'intérieur

## Configuration FID

Si votre application n'est pas en lien avec la FID, supprimez le fichier 

    config/Local/fid-default-headers.properties

## Configuration base de données 

Dans le fichier config/Local/database.properties, saisissez les informations d'accès à votre base de données de dev.

## Persistence.xml

Modifiez le fichier src/main/resources/META-INF/persistence.xml pour adapter le nom de la "Persistence Unit" dans le tag &lt;persistence-unit&gt;. Par défaut, c'est "sk-pu". Changez le pour mettre le nom de votre base.

Si vous avez une autre base de données, ajoutez un autre tag &lt;persistence-unit&gt; avec un nom différent.

N'oubliez pas de déclarer vos entités dans le tag &lt;persistence-unitgt; auquel elles correspondent.

## EntityManagerProducer

Renommez le fichier src/main/java/com/apave/acme/dao/impl/StarterKitEntityManagerProducer en AcmeEntityManagerProducer.

A l'intérieur, vous allez devoir créer autant de méthode que de base de données sur ce modèle :

    @Produces
    @RequestScoped
    public EntityManager createEntityManager() {
        LOGGER.atDebug().log("Création d'un EntityManager");
        return this.producer.createEntityManager("acme-pu");
    }

L'objectif est de produire un EntityManager qui pourra être injecté par CDI. Notez bien l'annotation @RequestScoped, qui garanti qu'un nouvelle EntityManager est créé à chaque requête http, mais surtout, que la méthode "dispose" sera appelée à la fin.

Passez bien par l'objet "EntityManagerProducer" (que CDI vous injectera) et utilisez sa méthode "createEntityManager" en lui passant le nom de la persistence unit. 

Si vous avez plusieurs bases de données, vous devrez créez plusieurs méthodes de ce type (on se moque totalement de leur nom !). Par contre, vous devrez leur ajouter des annotations personnalisées pour les distinguer.

Notez bien que dans la suite du code (dans les DAOs), vous pourrez vous faire injecter le (ou les) EntityManager(s) via un @Inject (et l'annotation personnalisée qui va bien si vous en avez plusieurs). Mais cela ne fonctionne que pour accéder à la base de donnée depuis une thread http, contrôlée par JAX-RS. Si vous être dans une thread à part, cela ne fonctionnera pas.

Pour cette raison, faites vous plutôt injecter un objet "EntityManagerProvider", qui - lui - fonctionnera dans les deux cas. Reportez vous à la classe com.apave.acme.dao.impl.TodoDaoImpl pour voir comment elle fonctionne.

# Renommer les packages

Commencez par renommer le package "com.apave.starterkit" en "com.apave.acme" (dans notre exemple).

Vous devez faire la modification à la fois dans le code principal, et dans les tests unitaires.

# Compilation par Jenkins

Le fichier "/Jenkinsfile" est un pipeline Jenkins, exécuté lorsque le code l'application évolue. Son but est à la fois de compiler le code, de faire remonter la version binaire dans Nexus, et de mettre à jour l'environnement de développement.

Apave met à disposition une bibliothèque Groovy permettant de lancer ces traitements en une fois. Vous devez seulement paramétrer l'appel initial en précisant le nom du projet.

Vous devez donc adapter le fichier "Jenkinsfile" pour votre projet

Modifiez la closure "customXlPackaging". Son objectif est de copier (dans le répertoire courant) tous les
fichiers que vous souhaitez voir remonter dans XL Deployer. La copie se fait via de simples appel bash.


    def customXlPackaging = {
        sh "cp target/monappli.war ."
        sh "cp config/xlDeploy/* ."
        sh "cp autrefichier.xml ."
        etc...
    }

Modifiez ensuite la dernière ligne pour définir :

- Le trigramme de votre application
- Le nom de l'environnement XL sur lequel déployer automatiquement

    pipelineJavaXL("<trigramme>", customXlPackaging, null, true, "Environments/SIUV2/DEV/<trigramme>/<trigramme>-ENV-DEV")

Laissez les arguments "null" et "true" en place.

# Déploiement XL Deploy

A l'intérieur, vous devez définir chacun des fichiers que vous allez déployer sur les environnements.
Vous devez faire référence aux fichiers copiés via le script Jenkins.

Et voilà !