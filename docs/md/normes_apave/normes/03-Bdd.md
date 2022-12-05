Hibernate est l'implémentation JBoss de JPA.

JPA et Jax-rs fonctionnent ensemble pour fournir au développeur un EntityManager qui permettra d'accéder à la base de données. Pour cela, JAX-RS créé un entity manager dès qu'il reçoit une requête http, et se charge de le fermer dès que la requête est terminée.

Mais du code qui s'exécute dans une autre thread (traitement asynchrone) ne pourra pas se faire injecter cet objet. Il ne pourra pas non plus ré-utiliser l'objet qui était à la disposition de la thread initiale (celle qui gérait la requête http) car JAX-RS l'aura fermé.

Pour cette raison, chez Apave, nous ne nous faisons JAMAIS injecter l'EntityManager en direct. A la place, Apave fourni un objet "EntityManagerProvider", qui possède une méthode get (à qui il est en plus possible de passer le nom de la Persistence Unit à utiliser).

Cet objet fonctionne aussi bien au sein d'une thread qui gère une requpete http, que dans le cas d'une autre Thread permettant un traitement asynchrone MAIS A UNE CONDITION: Cette thread aura du être instanciée à partir de l'ExecutorService fourni par Apave !

# Déclaration

Vous allez devoir déclarer vos bases de données dans deux fichiers.

Le premier est le fichier META-INF/persistence.xml. A l'intérieur, vous devez déclarer autant de &lt;persistence-unit&gt; que de bases de données. Donnez leur à chacun un nom bien défini.

Voici un exemple venant du starter kit :

    <?xml version="1.0" encoding="UTF-8" ?>
    <persistence 
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://java.sun.com/xml/ns/persistence 
            http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
        version="2.0" 
        xmlns="http://java.sun.com/xml/ns/persistence">
      <persistence-unit transaction-type="RESOURCE_LOCAL" name="sk-pu">
        <class>com.apave.starterkit.entity.TodoItem</class>
      </persistence-unit>
    </persistence>

N'oubliez pas que vous aller devoir déclarer chacune de vos entités à l'intérieur (dans le bon persistence unit). Habituellement, les IDE ne font automatiquement.

Vous devrez ensuite modifier le fichier database.properties (ou n'importe quelle autre fichier chargé par DeltaSpike). Chaque clé nécessaire à JPA devra être préfixée de la valeur "database.&lt;nom pu&gt;.". Voici l'exemple du database.properties présente dans le starter kit lorsqu'il est exécuté en local par un développeur :

    database.sk-pu.hibernate.connection.url=jdbc:h2:mem:mydatabase
    database.sk-pu.hibernate.connection.driver_class=org.h2.Driver
    database.sk-pu.hibernate.connection.username=
    database.sk-pu.hibernate.connection.password=
    database.sk-pu.hibernate.dialect=org.hibernate.dialect.H2Dialect

Cet exemple défini les informations de connexion pour le persistence unit nommé "sk-pu" (et déclaré dans le persistence.xml).

# Utilisation dans le code

Le code devra dans un premier temps "produire" (au sens CDI) un EntityManager par base de données. Voici un condensé du code présent dans le Starter Kit :

    @ApplicationScoped
    public class StarterKitEntityManagerProducer {
        @Inject EntityManagerProducer producer;
    
        @Produces @RequestScoped
        public EntityManager createEntityManager() {
            return this.producer.createEntityManager(SK_PU);
        }
        
        public void closeEntityManager(@Disposes EntityManager entityManager) {
            this.producer.closeEntityManager(entityManager);
        }
    }

Si vous avez plus d'une base de données, vous allez devoir créer des annotations personnalisées. Google est vitre ami pour avoir plus de détail.

A l'intérieur des DAOs, vous ne devez plus vous faire injecter les EntityManager directement, à moins que vous ne soyez absolument sûr que votre code ne sera JAMAIS exécuté par une thread qui ne correspond pas à une requête http.

A la place, vous devez vous faire injecter un "EntityManagerProvider". Voici un condensé du code de l'implémentation du DAO d'accès à la base de données des Todo :

    public class TodoDaoImpl implements ITodoDao {
        @Inject EntityManagerProvider em;
        
        @Override
        public List<TodoItem> getAll() {
            return em.get("sk-pu")
                    .createQuery("SELECT t FROM TodoItem t", TodoItem.class)
                    .getResultList();
        }
    }

Notez l'appel à la méthode "get" de l'EntityManagerProvider. Grâce à lui, ce code fonctionne aussi dans le contexte d'une thread parallèle.
