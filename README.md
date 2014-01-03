GLPI-ApacheAuthDBD
==================

Plugin GLPI pour l'authentification Apache via le mod_authn_dbd

GLPI utilise une base de données MySQL. Il stocke les mots de passe de façon chiffré au format SHA1 sous forme de chaine hexadécimal de 40 caractères (fonction php SHA1(password,false)).
Apache dispose de module d'authentification capable de lire une base de données MySQL et prennant en charge le format SHA1.
Cependant, Apache attend le SHA1 au format binaire, sous forme de chaine de 20 caractères. De plus, cette chaine doit être au format Base64.

Ce plugin effectue les opérations suivantes :

1. Le mot de passe SHA1 au format hexadécimal est converti en binaire par une commande php pack().
2. Le mot de passe est encodé en base64 par une commande php base64_encode().
3. Le mot de passe est prefixé avec la chaine {SHA} afin de permettre à Apache d'identifier l'encodage.


La table de données de GLPI n'est pas alterée pour permettre le fonctionnement du logiciel, les mots de passe au format "Apache" sont stockées dans une table additionnelle.
A l'installation du plugin, les mots de passe présent dans la base de données sont convertis.
Lorsque l'utilisateur met à jour son mot de passe, la version "compatible" Apache l'est aussi.
Lorsqu'un utilisateur est supprimé, la version "compatible" Apache l'est aussi.

Exemple de paramètrage Apache :

DBDriver mysql
DBDParams "host=monip port=3306 dbname=dbglpi user=userglpi pass=passdbglpi"
DBDExptime 300
DBDMin 1
DBDMax 10

<Directory "/var/www/web">
AuthType basic
AuthName "Registered User"
AuthBasicProvider dbd
AuthDBDUserPWQuery "SELECT glpi_plugin_apacheauthdbd_users.password FROM glpi_plugin_apacheauthdbd_users INNER JOIN glpi_users ON (glpi_users.id = glpi_plugin_apacheauthdbd_users.users_id AND glpi_users.is_active=1) WHERE glpi_users.name=%s AND glpi_plugin_apacheauthdbd_users.auth=1" 
Require valid-user
</Directory>
