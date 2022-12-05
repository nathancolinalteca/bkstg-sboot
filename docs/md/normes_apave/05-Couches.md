Par couches, on entend "packages" et modules maven.

# La couche Config







# Les couches DAO et Entity

Pas de code dans la couche DAO.
Pas besoin d'interfaces pour la couche DAO (car presque pas de code dedans).
Pas de tests unitaires, mais des tests d'intégration.

Implémentation des entité et des implémentations dans un module maven "-dao".

# Les couches Service et DTO

Objets Service implémentés dans un package "service".
Objets DTO implémentés dans un package "dto".
Les objets service n'ont le droit de prendre en entrée et de retourner que des objets de la couche DTO.
Des objets de type converter sont chargés de convertir les DTO en entité.

100% du code métier dans cette couche. Taux de couverture par les tests le plus proche possible de 100%.

Utilisation d'interfaces

ATTENTION: Cas particulier pour la gestion des versions. Se reporter à la doc dédiée.

# La couche Rest

Lien entre package et chemin Rest recommandé : "com.apave.starterkit.rest.interne" => /interne

sous package "exposee": API exposées à d'autres applications. Doit être versionné.
	=> Ne pas trop d'étendre sur les versions. Traité dans un doc dédié (ajouter un lien)

sous package "interne": 
- Services Rest à disposition du front. 
- Ce code évoluera en même temps que le front. 
- On ne gère pas de version. 
- Ces services sont accessibles par les utilisateurs en direct depuis leur navigateur
- Ils doivent donc être protégés par la FID. 
- Services accessibles en "/interne"

Logging du début et fin des appels à chaque méthode.
FIXME: On devrait faire ça via AOP.

FIXME: Les deux services "obligatoires" (InfosRest et UserInfosRest) devraient être déclaré dans un jar à part.

# La couche Converter

Contient les objets de type "converter" qui permettent de passer d'un DTO à une entité et inversement.

Contient aussi des converters pour la gestion des versions. Se reporter à la doc dédiée.
