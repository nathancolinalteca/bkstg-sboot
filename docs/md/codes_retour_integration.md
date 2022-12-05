# Codes de retour pour l'intégration des données Sercel

Ces codes sont définis dans la classe `CodeResultatIntegrationGlobal`
* **ERR01** : Une erreur technique (timeout…) empêche la connexion à Sercel lors du login ou de la récupération de la liste d'ouvrages.
* **ERR02** : Le login a échoué. La connexion à Sercel a réussi, mais un code d'erreur HTTP 400 ou 500 a été reçu en réponse à la tentative de login.
* **ERR03** : La récupération de la liste des structures a échoué. Le format de données reçu de Sercel lors de la récupération de la liste des structures n'est pas celui attendu.
* **ERR04** : La récupération de la liste des structures a échoué. Un code d'erreur HTTP 400 ou 500 a été reçu en réponse à la tentative de récupération de la liste de structures.
* **ERR05** : Le traitement a globalement réussi, mais une ou plusieurs structures sont en erreur. L'id des structures en question est énuméré dans la suite du message d'erreur.
