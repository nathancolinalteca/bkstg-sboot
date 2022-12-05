Weld est l'implémentation JBoss de CDI2. Il vous permet d'utiliser les annotations classiques comme @Inject, etc...

Aucun code n'est nécessaire pour l'activer. Il suffit que les jars de Weld soient dans le war.

- @Inject Injecter une bean
- @Produces @Disposes
- @RequestScoped @ApplicationScoped @Singleton
- @Qualifier

META-INF/beans.xml si jar externe