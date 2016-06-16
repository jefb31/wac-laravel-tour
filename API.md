# Avant de commencer

### Pré-requis : PHP, Apache, MySQL, Composer.

Nous allons utiliser Laravel pour la partie API.

Assurez-vous d'avoir Composer d'installé, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM pour Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Une fois l'installation terminé, un dossier "api" vient d'être créer : déplacer le à la racine de votre serveur web.

Commencer par créer une base de donnée avec phpMyAdmin ou votre terminal.

```sh
mysql -u<username> -p<password>
CREATE DATABASE <dbname>
```

Assurez-vous maintenant de donner les droits nécessaires au dossier "api/storage" : un CHMOD 755 suffira.

Editer le fichier "api/.env", coeur de configuration de Laravel, pour y insérer vos informations de connexion MySQL.


### Etape 1 : Migration

La première étapes va être de modéliser notre base de donnée.

Laravel intègre un outil de migration qui permet de créer votre base de donnée via des classes PHP.

Finit les longues minutes sur phpMyAdmin à sélectionner des listes et remplir des cases.

L'avantage de ce système est triple :

- Les autres développeurs pourront voir tout changement sur la base donnée via le code
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback)
- Les migrations sont partagées lorque vous faîtes un commit, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans demander un dump

Ouvrez le dossier "api/database/". 3 dossiers sont présents :

- Factories : Permet de définir des factory pour créer des modèles pré-définis et d'utiliser Faker pour remplir les données.
- Migrations : C'est ce qui nous intéresse, c'est ici que l'on code les migrations. 1 fichier = 1 migration.
- Seeds : Permet de remplir sa base de donnée, tout simplement.

Ouvrez le fichier "api/database/migrations", 2 migrations sont déjà présente. Par défaut, Laravel intègre l'authentification et les migrations qui vont avec.

Lorsque vous lancez la commande "php artisan migrate" dans votre terminal, Laravel execute les migrations. En revanche, si vous faîtes un "php artisan rollback", vous revenez en arrière. Très simple.

Supprimez les 2 migrations de base, nous ne les utiliseront pas.

Utiliser la commande "php artisan make:migrate createUsersTable" pour créer une nouvelle migration. Le fichier se créer et est pré-rémplit, vous n'avez plus qu'a coder !

Vous allez devoir réfléchir à la structure de la base de donnée pour 5 tables :

- Users
- Films
- Categories
- Partners
- Tickets

Executer la commande présente en personnalisant le dernier paramètre pour créer vos premières migrations.

Certaines tables ont des relations qu'il faudra penser en amont.

Voici un exemple de table Users :

```php
 Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('login', 10);
            $table->string('name', 20);
            $table->string('last_name', 20);
            $table->string('email')->unique();
            $table->string('password');
            $table->date('birthday');
            $table->timestamps();
        });
```

La méthode "timestamp" permet de dire à Laravel qu'on a besoin de créer deux champs supplémentaires : created_at, updated_at, qui porte respectivement leur noms.
