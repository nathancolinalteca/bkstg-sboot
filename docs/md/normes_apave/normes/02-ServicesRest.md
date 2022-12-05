Jersey est l'implémentation Oracle de JAX-RS. C'est la seule brique Oracle présente dans le starter kit. Idéalement, pour être cohérent, il faudrait la remplacer la Rest-Easy, l'implémentation de JBoss.

Jersey doit être initialisé via la déclaration d'une servlet dans le fichier web.xml. 

    <servlet>
        <servlet-name>jersey-serlvet</servlet-name>
        <servlet-class>
            org.glassfish.jersey.servlet.ServletContainer
        </servlet-class>
        <init-param>
            <param-name>jersey.config.server.provider.packages</param-name>
            <param-value>com.apave</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>jersey-serlvet</servlet-name>
        <url-pattern>/rest/*</url-pattern>
    </servlet-mapping>

Le paramètre "jersey.config.server.provider.packages" est très important. C'est lui qui dit à jax-rs où trouver ses classes (pouvant être injectées via CDI). Laissez donc la valeur "com.apave".

Apave impose que cette servlet soit mappée sur les urls du type "/rest/*". C'est très important car de nombreux filtres nécessaire au fonctionnement du logging, des traitements en parallèle, etc... dépendent de cette configuration.





# Bean validation

@Valid
@Size
@NotNull

Depuis JAX-RS: Automatique, avec un exception mapper en plus
Depuis le code: Via les API validation
