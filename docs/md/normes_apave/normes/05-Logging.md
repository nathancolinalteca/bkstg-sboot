# Log4j et ELK

## Dépendance Maven

Pour initialiser le logging, l'application doit dépendre du module maven apave-generic-log :

    <dependency>
        <groupId>com.apave.stsi.generic</groupId>
        <artifactId>apave-generic-log</artifactId>
        <version>Pourra évoluer dans le temps...</version>
    </dependency>

Cette dépendance va apporter :

- Des filtres Servlet et JAX-RS: Ils se chargeront d'extraire les informations des headers http, et de la configuration de l'application.
- Une extension log4j2 permettant de faire référence aux patterns %Context et %JsonMessage dans la configuration log4j2.
- Et des classes pour logger des informations spécifiques (qui finiront en JSON).

ATTENTION: Cette dépendance va fournir au développeur les APIs log4j2. L'implémentation (dont le développeur n'a pas besoin pour travailler) sera fournie par le module "apave-generic-j2ee-impl".

## Définition du format de log

Reportez vous au fichier log4j2.xml présent dans le répertoire config/Local pour plus de détail.

L'extension log4j2 fournie par cette dépendance est activée par la propriété du tag racine :

    package="com.apave.stsi.generic.logger"

Elle vous permet d'utiliser les syntaxes suivantes lorsque vous définissez un pattern de logging :

    %Context{UN-HEADER-HTTP}
    %Context{UNE-PROPRIETE-DE-CONF}
    %JsonMessage{SPEC / REF}

%Context prend en paramètre aussi bien un nom de header http que le nom d'une propriété définie dans un fichier properties chargé par delta spike.

%JsonMessage log en JSON un éventuel objet associé à un message de logging (API spécifique Apave).
REF fait référence à un objet utilisant les propriétés définies dans le référentiel, alors que SPEC est
totalement ouvert (le développeur met les propriétés qu'il veut dedans)

## Lien avec le module concurrent

Lorsque vous lancez une tâche en parallèle, elle n'est plus liée à une requête http. Cette dernière se sera probablement terminée lorsque vous essayerez de logguer. Des informations comme l'id de corrélation ou le nom de l'utilisateur courant ne seront donc plus accessibles.

Le module apave-generic-concurrent tient compte du logging. Il va donc vous reportez automatiquement toutes ces informations. En conclusion, si vous utilisez le module apave-generic-concurrent, il n'y aura pas d'impact, et ce sera totalement transparent.

## Logger un message sans spécificités (sans JSON)

Il suffit d'utiliser les APIs log4j2 standard.

Créez un Logger en statique sur la classe courante :

    private static final Logger LOGGER = LogManager.getLogger(MaClass.class);

Les classes doivent être importées de la manière suivante :

    import org.apache.logging.log4j.LogManager;
    import org.apache.logging.log4j.Logger;

Puis, utilisez la syntaxe log4j2 standard :

    LOGGER.atInfo().log("ID Ged Apave {} invalide", idGedApave);

Ou bien, s'il y a une exception en plus :

    try {
        ....
    } catch(Exception e) {
        LOGGER.atError().log("L'URL {} est inaccessible", url).withThrowable(e);
    }

## Logger un objet JSON en plus du message

Ajouter du JSON à un message de log permet de remonter des champs spécifiques dans l'index Elastic. Ces champs pourront ensuite être utilisés pour générer des Dashboards Kibana.

### Le référentiel et sa version

Lorsque deux applications vont logger une même information, elles devront le faire en utilisant le même nom de propriété. L'ensemble des noms de propriétés communes est défini dans un référentiel fourni et maintenu par le pôle production (qui est en charge de la stack ELK). Voici son URL (TODO) :

[Référentiel ELK]()

Ce référentiel évoluera dans le temps. Il est donc versionné. Le numéro de version du référentiel utilisé par votre application doit être déclaré dans la propriété "app.refVersion".Par défaut, elle se défini dans le fichier com/apave/config/app.properties. Mais elle peut l'être au travers de n'importe quel autre fichier, du moment qu'il est chargé par delta spike.

    app.refVersion=1.0

Ensuite, pour ajouter un objet à la ligne de log, il faut commencer par comprendre la syntaxe Log4j2. Ces deux morceaux de code sont 100% identiques :

    LOGGER.atInfo().log(
        "ID Ged Apave {} invalide", 
        idGedApave
    );

Et 

    LOGGER.atInfo().log(
        new FormattedMessage(
            "ID Ged Apave {} invalide", 
            idGedApave
        )
    );

Pour ajouter un objet (qui sera transformé en JSON) à la ligne de log, vous devez utiliser la classe JsonMessage au lieu de FormattedMessage (JsonMessage étend FormattedMessage), et appeler sa méthode "withJson" :

    public class MonObjet extends JsonLog {
        @JsonProperty("exemple.propriete_1") private String propriete1;
        public String getPropriete1() { return this.propriete1; }
        public MonObject setPropriete1(String propriete1) { this.propriete1 = propriete1; return this; }
        
        @JsonProperty("exemple.propriete_2" private int propriete2;
        public int getPropriete2() { return this.propriete2; }
        public MonObject setPropriete2(int propriete2) { this.propriete2 = propriete2; return this; }
    }
    
    LOGGER.atInfo().log(
        new JsonMessage(
            "ID Ged Apave {} invalide", 
            idGedApave
        )
        .withRef(
            new MonObjet()
            .setPropriete1("chaîne")
            .setPropriete2(10)
        )
    );

Notez les éléments suivants :

- La classe "MonObjet" étend la classe "JsonLog". Cela permet d'ajouter automatiquement le numéro de version défini dans la propiété "app.refVersion".
- La classe "MonObjet" sera serialisée en JSON par Jackson. Vous pouvez donc utiliser toutes les annotations habituelles.
- L'utilisation de l'objet JsonMessage, extactement de la même manière que FormattedMessage
- L'utilisation de la méthode "withJson" pour ajouter l'objet à la ligne de log.

Notez qu'il est aussi possible de passer par une Map au lieu d'un objet :

    Map<String, Object> map = new HashMap<>();
    map.put("propriete1", "chaîne");
    map.put("propriete2", 10);
    
    LOGGER.atInfo().log(
        new JsonMessage(
            "ID Ged Apave {} invalide", 
            idGedApave
        )
        .withRef(map)
    );
    
Java 11 nous donnerait une syntaxe pour écrire la Map en ligne... mais on est bloqué en Java 8.

### Un objet spécifique

C'est exactement la même chose que pour l'objet de référence sauf que :

- On utilise la méthode withSpec au lieu de withRef
- Et il n'y a pas de version dedans

