# Avant de commencer

### Pré-requis : PHP, Apache, MySQL, Composer.

Nous allons utiliser Laravel pour la partie API.

Le framework hérite des composants de Symfony et permet de lancer un projet rapidement sans trop formaliser et en gardant une certaine liberté pour le développeur. De plus, il est demandé sur le marché et permet de se familiariser avec la structure d'un framework.

Assurez-vous d'avoir Composer d'installé sur votre machine, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM pour Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel depuis Composer et créer un nouveau projet :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Une fois l'installation terminé, un dossier "api" vient d'être créer : déplacer-le à la racine de votre serveur web.

Passons maintenant à l'étape crucial : la création de la base de donnée depuis phpMyAdmin ou votre terminal.

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

- Les autres développeurs pourront voir tout changement sur la base donnée via le code et retracer l'historique des changements.
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback), plutôt pratique si l'on se trompe.
- Les migrations sont partagées lorque vous faîtes un commit, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans demander un dump, juste en executant à leur tour les migrations.

Ouvrez le dossier "api/database/". 3 dossiers sont présents :

- Factories : Permet de définir des factory pour créer des modèles pré-définis et d'utiliser Faker pour remplir les données.
- Migrations : C'est ce qui nous intéresse, c'est ici que l'on code les migrations. 1 fichier = 1 migration.
- Seeds : Permet de remplir sa base de donnée, tout simplement. Utile si vous souhaitez insérer de fausses données.

Ouvrez le fichier "api/database/migrations", 2 migrations sont déjà présente.

Par défaut, Laravel intègre l'authentification et les migrations qui vont avec.

Lorsque vous lancez la commande "php artisan migrate" dans votre terminal, Laravel execute les migrations.

En revanche, si vous faîtes un "php artisan rollback", vous revenez en arrière, logique.

Supprimez les 2 migrations de base, nous ne les utiliseront pas.

Utiliser la commande "php artisan make:migrate create_users_table" pour créer une nouvelle migration.

Vous allez devoir réfléchir à la structure de la base de donnée pour 5 tables :

- Users
- Films
- Categories
- Partners
- Tickets

Executer la commande çi-dessus pour créer une migration par table, en personnalisant le dernier paramètre.

Certaines tables ont des relations qu'il faudra penser en amont.

Chaque migration possède une méthode up() et down(). Vous l'aurez compris, up() et down() ont bien un rapport avec "php artisan migrate" et "php artisan rollback".

Voici un exemple de méthode up() pour la migration Users :

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
  $table->softDeletes()
});
```
La méthode "timestamp" permet de dire à Laravel qu'on a besoin de créer deux champs supplémentaires : created_at, updated_at, qui porte respectivement leur noms. "softDeletes" permet de préciser à Laravel qu'on ne supprimera pas vraiment l'utilisateur, un système d'état actif/inactif est plus approprié pour conserver les données.

Voici un exemple de méthode down() pour la migration Users :

```php
Schema::drop('users');
```

Avant de passer à l'étape suivante, il est nécessaire de créer les migrations pour les 5 tables et d'exécuter la commande "php artisan make:migrate".

Assurez-vous de penser aux relations.

### Etape 2 : Routes

### Etape 3 : Models

### Etape 5 : Gestion d'erreur

### Etape 6 : Users et Authentification

### Etape 7 : Films et Categories

### Etape 8 : Tickets et Partners
