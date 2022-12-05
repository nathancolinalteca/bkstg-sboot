# Initialisation de la politique de surveillance par défaut

## Comment la politique de surveillance par défaut est-elle stockée ?
- Nous avons choisi de stocker les valeurs par défaut pour l'initialisation d'une politique de surveillance dans 
  la base de données sous forme d'une politique de surveillance qui n'est associée à aucun ouvrage d'art. (T_PSU_POLITIQUE_SURVEILLANCE.OAT_ID = NULL)
- Uniquement les colonnes qui avaient une valeur par défaut seront renseignées les autres resterons nulle.
- Une ligne sera enregistrée dans la table `t_psu_politique_surveillance`.
- Une ligne est enregistrée dans la table `t_sls_seuils`.
- Une ligne pour chaque type de Kpi sera enregistrée dans la table `t_dsl_details_seuils`.
- Une ligne sera enregistrée dans la table `t_alp_ratio_alpha`.
- Deux lignes dans la table de paramétrage de la surveillance `t_sur_surveillance` (une pour le mode renforcé, une pour le mode normal)

## Comment mettre à jour la politique de surveillance par défaut ?
- Pour administrer / metre à jour la politique de surveillance, il faut chercher dans la base de données la politique
  de surveillance qui n'est associée à aucun ouvrage d'art (idOuvrage est nulle) et modifier ses valeurs.
  
## Quand est utilisée la politique de surveillance par défaut ?
- La politique de surveillance est utilisée lorsque l'utilisateur créé un ouvrage. L'application doit créer une politique de surveillance avec des valeurs par 
  défaut et l'associer à celui-ci. 
- La politique de surveillance est créée à partir de celle enregistrer dans la base de données, en faisant une copie et en 
  remplissant les valeurs manquantes.
  
## Comment fonctionne le clonage de la psu par défaut ?
Le clonage se fait par introspection. Tous les champs de l'entités à cloner sont trouvés ainsi que leurs getters / setters. Le nouvel objet est rempli en "invokant" les setters sur un nouvel objet.

Sont effectués pendant le processus:
- Clonage de la politique de surveillance par défaut et remplissage des valeurs des champs manquantes 
- Clonage de l'entité SeuilsEntity et remplissage des champs manquants , puis l'associer à la politique de 
  surveillance qui vient d'être créée.
- Clonage de chacune des entités detailsSeuilsEntity plusieurs fois en fonction du typeKpi, et du nombre des capteurs et 
  des modes de l'ouvrage d'art puis les associer à l'entité SeuilsEntity qui vient d'être créée.
- Clonage de chacune des entités ratioAlphaEntity plusieurs fois en fonction du nombre des modes, puis les associer 
  à la politique de surveillance qui vient d'être  créée.
- Clonage du paramétrage de la surveillance.  
