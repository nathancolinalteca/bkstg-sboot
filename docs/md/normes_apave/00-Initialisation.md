Ce document décrit la procédure de démarrage d'un nouveau projet

# GitLab

Au démarrage de chaque projet, le code du starter kit doit être copié dans un nouveau dépôt sur le serveur GitLab. L'emplacement du dépôt doit être cohérent avec le POS (Plan d'Occupation des Sols). Il doit donc être déterminé au démarrage par l'architecte du projet. 

La copie sera réalisé à partir de ces informations par le CTA de la SAS responsable du projet.

Notez bien qu'on parle de copie, et pas de fork. Récupérer l'historique ne sert à rien car les adaptations à réaliser dans le code seront trop importantes pour espérer fusionner les modifications apportées au starter kit lui même.

# Jenkins

De la même manière, un job Jenkins doit être créé pour le projet. Ce job sera en charge de scruter le repository git, et de lancer des builds automatiquement lorsqu'une des branches évolue.

Le Job Jenkins sera aussi chargé de déployer automatiquement le projet sur l'environnement de développement grâce à XLDeploy.

Il s'agira d'un Job de type "Multibranche Pipeline" qui surveillera toutes les branches nommées "RELEASE_*", ainsi que la branche "develop".

Le job doit se nommer "\[Trigramme]\[Nom appli]\[Id composant]\[Zone]" :

- Le trigramme et le nom de l'application sont fournis par l'architecte
- L'id du composant est de la forme (en minuscule) "trigramme-type-discriminant" :
    - trigramme : le même que celui de l'application, mais en minuscule
    - type: L'un des types existants :
        - be: Back end
        - fr: Front end
        - lib: Bibliothèque
        - dbb: Scripts SQL
        - plug: Plugin
        - etc...
    - discriminant: Un discriminant qui permet de nommer le composant.
- Zone: Le code de la zone (ZSU, ZES, ZPA, ZNO ou APPLI) 

Comme pour le repository GitLab, c'est le CTA de la Sas qui sera chargé de générer le job.

### Lien Gitlab vers Jenkins

Une fois le repo Gitlab initialisé, et le job Jenkins créé, le CTA va configurer le repository git pour qu'il lance le job Jenkins automatiquement lorsqu'un développeur pousse des modifications.

Allez dans la partie Settings/Intégration, et renseignez dans l'url: 

    http://jenkins-pic.apave.grp:7792/project/<nom du job jenkins>

Cliquez ensuite sur le bouton vert (un peu plus bas) "Add Webhook".