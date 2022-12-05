Apave impose un taux de couverture du code de minimum 60%. Mais ce chiffre en lui même n'est pas important : C'est une moyenne à laquelle les développeurs arriver s'ils respectent ces quelques bonnes pratiques :

- Ne pas tester ni la couche DAO, ni la couche Rest, mais *ne pas mettre d'algorithmie dedans non plus* (pas de "for", pas de "if").
- Tester à 100% (ou à un taux le plus proche possible de 100%) la couche service.

Ne pas respecter ces deux recommandations est considéré comme *bloquant*.

# Lien maven

Techniquement, en héritant du pom.xml parent Apave (com.apave:root-java), le projet hérite automatiquement de :

- JUnit 5 pour l'exécution des tests unitaires
- AssertJ pour avoir une syntaxe d'assertions plus complète que celle fournie par JUnit de base.
- Et Mockito pour povoir mocker les dépendances.

Sur le site Baeldung, on retrouve un ensemble d'articles sur la manière d'utiliser Mockito :

    https://www.baeldung.com/mockito-series

# Exécution de tests d'intégration

Le pom.xml racine va demander à Maven de n'exécuter que les tests nommés 

    - "*Test"
    - "*Tests"
    - "Test*"
    - "*TestCase"

Ainsi, si vous voulez créer des tests d'intégration (pour vérifier le bon fonctionnement de vos DAOs par exemple), il vous suffit de créer des classes nommées en "\*IT" ou "IT\*".

# Intégration avec Deltaspike

Deltaspike n'est pas une API J2EE. Elle n'est pas comprise par Mockito. Cela signifie que lorsqu'une bean se fait injecter une propriété dont la valeur vient de la configuration, Mockito ne fera rien.

La solution est alors très simple : 

- Définissez bien la propriété injectée en "package private" (ni private, ni public, ni protected)
- Définissez la valeur dans une méthode @BeforeEach

Voici un exemple de code :

    01 public class MonService {
    02   @Inject @ConfigProperty(name = "param") int param;
    03   @Inject IMonDao dao;
    04
    05   etc...
    06 }

*01* : Injection de la valeur depuis la configuration.

La classe de test aura cette forme :

    01 public class MonServiceTest {
    02   @Mock IMonDao dao;
    03   @InjectMocks MonService svc;
    04
    05   @BeforeEach void setup() {
    06     MockitoAnnotations.initMock(this);
    07     this.svc.param = 10;
    08   }
    09
    10   etc...
    11 }

*02* : On mock un DAO "normalement"
*03* : L'objet dans lequel seront injectés les mocks... mais pas les propriétés DeltaSpike...
*06* : Demande à Mockito de traiter toutes les propriétés
*07* : Renseigne la valeur pour le paramètre
