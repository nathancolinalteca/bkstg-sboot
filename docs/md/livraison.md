# Packaging et déploiement

## Checklist backend

- [ ] Mettre à jour le MPD dans la documentation (le générer via le datamodeler d'oracle sql developper)
- [ ] Finir les merges requests à inclure sur develop
- [ ] Vérifier que le pipeline sur develop se passe bien (verify)
- [ ] Merger develop dans master
- [ ] Tag git (en replissant succintement la release note, voir exemples) à partir de master
- [ ] Via la CI, laisser l'analyse tourner, puis packaging alteca, puis déploiement interne
- [ ] Tester les fonctionnalités
- [ ] Télécharger le zip des sources de la branche master
- [ ] Télécharger l'artifact de la CI pour les scripts SQL et l'openAPI
- [ ] Sur develop, incrémenter la version (mvn versions:set -DnewVersion=XXX && mvn versions:commit) pour la prochaine livraison
- [ ] Mettre à jour le numéro de version dans la conf des logs: config/xlDeploy/log4j2-metier et log4j2-traitement.xml (dans les pattern)

## Checklist frontend

- [ ] Finir les merges requests à inclure sur develop
- [ ] Vérifier que le pipeline sur develop se passe bien
- [ ] Merger develop dans master
- [ ] Tag git (en replissant succintement la release note, voir exemples) à partir de master
- [ ] Via la CI, laisser la config tourner, puis déploiement interne
- [ ] Tester les fonctionnalités
- [ ] Télécharger le zip des sources de la branche master
- [ ] Sur develop, incrémenter la version (toujours laisser le snapshot)

## Performances
Pour l'instant, on est sur un "best effort" pour livrer un rapport gatling.
- [ ] En cas de modification du projet gatling, on livrera zippé le rapport en html

## Base de données
- [ ] Récuperer le dossier oracle dans l'artifact généré par la CI backend.
- [ ] Rejouer les scripts sur un oracle (local ou ads.alteca.fr), prévoir d'appliquer un dump de la version précédente pour gagner du temps
- [ ] Dump la base pour pouvoir la remonter facilement 
- [ ] Ajouter les scripts de la version dans aps-bdd sur master.
- [ ] Télécharger le zip des sources de la branche master    

## Finalisation

- [ ] Préparer un dossier pour la version
- [ ] Backend: Déplacer dans ce dossier l'archive aps-backend-master.zip téléchargé
- [ ] Swagger: Ajouter le fichier swagger en json se trouvant dans l'artifact téléchargé pour le back
- [ ] Front: Ajouter dans le dossier le aps-front-master.zip téléchargé
- [ ] DB: Ajouter dans le dossier le aps-bdd-master.zip téléchargé
- [ ] Mettre à jour les git sur le pc client (3 projets)  

## Côté client:
- [ ] Pour chaque projet, créer une branche `feature_version` (ex: feature_1.0.5) à partir de develop.
- [ ] Supprimer le projet, sauf le .gitignore
- [ ] Copier / coller le contenu du .zip du projet dedans, et push le nouveau code.
- [ ] Vérifier sur le jenkins du client que le pipeline de la branche se passe bien
- [ ] Si pipeline OK, merger dans develop et supprimer la branche
