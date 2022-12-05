# Couche Rest et DTO

Les services versionnés doivent être implémentés dans le package "exposee".

- Services rest exposés à d'autres applications. 
- On garanti la compatibilité ascendante via une gestion de versions. 
- Ces services ne doivent pas pouvoir être appelés par un poste utilisateur. 
- La FID ne supportant pas le back to back, ces services ne sont pas protégés. 
- Services accessibles en "/exposee/<version>/".
- Numéro de la version dans le nom de la classe (mieux que dans des packages. Evite les noms de classes en double).
- DTO dans un package à part, avec le numéro de version dans le nom des classes.
- Converter pour transformer les DTO d'une version à l'autre.
- Pas de code dans cette couche. On appel un service dédié et versionné aussi.

# Couches Service et DTO

Les DTOs des versions précédentes doivent être implémentés dans le package "dto".
Le nom des classes doit contenir le numéro de la version lorsque c'est nécessaire.
Si une DTO n'a pas changé entre deux versions, pas besoin de le re-créer.
Convertion d'une version à une autre via un converter.  

Un objet Service, non numéroté, correspondant à la dernière version (version courante).

Un objet service par version hors version courante, avec le numéro de version dans le nom de la classe. Chacun de ces objets n'a le droit que de se faire injecter le service courant (et d'éventuels converters).
