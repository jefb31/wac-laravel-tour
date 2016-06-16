# Avant de commencer

### Pré-requis : PHP, Apache, MySQL, Composer.

Nous allons utiliser Laravel pour la partie API. Le framework permet de faire des API très rapidement et possède une syntaxe très clair.

Assurez-vous d'avoir Composer d'installé, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM pour Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Si l'installation foire, les messages d'erreurs sont là pour vous aider.

Une fois l'installation terminé, un dossier "api" vient d'être créer : déplacer le à la racine de votre serveur web.

Commencer par créer une base de donnée avec phpMyAdmin ou la console.

```sh
mysql -u<username> -p<password>
CREATE DATABASE <dbname>
```

Assurez-vous maintenant de donner les droits nécessaires au dossier "api/storage" : un CHMOD 755 suffira.

### Etape 1 : Migration

La première étapes va être de modéliser notre base de donnée.

Laravel intègre un outil de migration qui permet de créer votre base de donnée via des classes PHP.

Finit les longues minutes sur phpMyAdmin à sélectionner des listes et remplir des cases.

L'avantage de ce système est triple :

- Les autres développeurs pourront voir tout changement sur la base donnée via le code
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback)
- Les migrations sont partagées lorque vous pushez, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans vous demandez un dump

Ouvrez le dossier "api/"
