
DelatSpike est un outil de la fondation Apache qui permet de gérer une externaliation de la configuration.

## Chargement des clés

La configuration en elle même peut être définie à de nombreux endroits. Chez Apave, on considére qu'elle doit l'être dans des fichiers ".properties", qui doivent eux même être stockés dans le répertoire "conf" de Tomcat. Cela fonctionne car chaque Tomcat n'accueil qu'un seul war. 

Pour rendre les clés présentes dans un fichier properties disponible à votre code, vous devez créer une classe qui étend l'objet abstrait "TomcatConfPropertyFileConfig", ce qui vous imposera d'implémenter une seule méthode. Voici un extrait du code du starter kit : Il permet de charger les clés contenues dans un fichier nommé "database.properties" :

    public class DatabasePropertyFileConfig extends TomcatConfPropertyFileConfig {
        @Override
        public String getTomcatConfFilename() {
            return "database.properties";
        }
    }

Vous pouvez créer autant d'objet qui pointent vers autant de fichiers que vous voulez.

## Utilisation des clés

Pou utiliser une clé définie dans un des fichiers chargés, vous devez utiliser une annotation :

  




Injection d'une propriété
