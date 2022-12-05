# Decisions prises pendant la phase projet

## Sauvegarde des seuils pour calcul des niveaux
Les seuils sont stockés dans une table unique pour:
 - les kpi locaux
 - la trace de la mac
 - la comac

Cette table possède une fk vers les modes et une vers les capteurs. Ces FK sont remplies à tour de rôle, les modes pour la mac et les kpi locaux, les capteurs pour la comac.
Lors que les ratios s'appliquent à la comac ou la mac, les informations utilisées sont celles des colonnes du mode de calcul ABSOLU.
