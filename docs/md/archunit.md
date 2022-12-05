# Tests ArchUnit
Tous les tests sont dupliqués dans l'api métier et l'api traitement. Bien penser à modifier les deux en cas d'évolution !
## Contrôle d'accès des couches
- APIs n'accèdent pas à l'infra
- Traitement n'accède pas à l'infra
- api accede a service 
- service accede a infra
- traitement accede à service
- common n'accede à rien

## Sécurité
### Controllers
Toutes les méthodes publiques de contrôlleurs doivent porter l'annotation `@PreAuthorize`

## Normes de codage

### Méthodes
- Une méthode publique ne doit pas avoir plus de 4 paramètres

### Champs
- Aucun champ ne doit être `@Autowired` excepté dans les classes de configuration

### Classes abstraites
Toutes les classes abstraites doivent avoir un nom commençant par `Abstract` et inversement

### Entités
Toutes les classes annotées par `@Entity` doivent :
- Se trouver dans le package `com.apave.apstructure.infrastructure.jpa.entity`
- Avoir un nom finissant par `Entity`
- Hériter de `AbstractEntity`
Toutes les classes ayant un nom finissant par `Entity` doivent être des entités.

### Repositories
Toutes les classes annotées par `@Repository` doivent :
- Se trouver dans le package `com.apave.apstructure.infrastructure.jpa.repository`
- Avoir un nom finissant par `Repo`
- Hériter de `JpaRepository`
Toutes les classes ayant un nom finissant par `Repo` doivent être des repositories.

### Dto
Toutes les classes se trouvant dans un package contenant `dto` doivent être des dto ou des enums
Tous les DTO doivent :
- Se trouver dans le package `com.apave.apstructure.common.dto` (sauf module récupération)
- Avoir un nom finissant par `Dto`
- Hériter de `AbstractDto`
Toutes les classes ayant un nom finissant par `Dto` doivent être des dto.

### Enums
Toutes les enums doivent se trouver dans le package `com.apave.apstructure.common.dto`

### Classes utilitaires
Toutes les classes se trouvant dans un package contenunt `utils` doivent être des classes utilitaires
Toutes les classes utilitaires doivent :
- Se trouver dans le package `com.apave.apstructure.common.utils`
- Avoir un nom se finissant par `Utils`
- N'avoir que des constructeurs privés
- N'avoir que des méthodes statiques

### Services
- Les services doivent être placés dans un package `..service..`
#### Nommage
- Les interfaces de services doivent avoir un nom terminant par `Service`
- Les implémentations de services doivent avoir un nom terminant par `ServiceImpl`
#### Annotations
- Les implémentations de services doivent porter l'annotation `@Service`
- Toutes les méthodes publiques de services doivent porter l'annotation `@Transactional`

### Mappers
- Les mappers doivent avoir un nom finissant par `Mapper`
- Les mappers doivent être dans un package `..service.mapper..`
- Les mappers doivent autant que possible, hériter de `AbstractMapper`
- Les mappers doivent porter l'annotation `@Component`

### Messaging
#### Publishers
Les publishers doivent : 
- Être dans un package `..application.messaging..publisher..`
- Être nommé `Publisher(Impl)`
- Implémenter `EventPublisher`
- Être annotés `@Component`
#### Consumers
Les consumers doivent :
- Être dans un package `..application.messaging..consumer..`
- Être nommé `Consumer(Impl)`
- Implémenter `EventConsumer`
- Être annotés `@Component`
Leurs méthodes publiques doivent porter les annotations `@EventListener` et `@Async`
####Events
Les events doivent : 
- Être dans le package `com.apave.apstructure.common.event`
- Avoir un nom finissant par `Event`
- Hériter de `AbstractEvent`

### Exceptions
Les exceptions doivent :
- Être dans le package `com.apave.apstructure.common.exception`
- Être nommées `APS…Exception`

### Api
Le package `api` ne doit contenir que des controllers, des requests ou des response.
Les controllers doivent :
- Avoir un nom finissant par `Controller`
- Porter l'annotation `@Controller`

Les méthodes publiques de controllers doivent toujours retourner une `ResponseEntity`

Les requests doivent :
- Avoir un nom finissant par `Request`
- Étendre AbstractRequest

Les responses doivent :
- Avoir un nom finissant par `Response`
- Étendre AbstractResponse
