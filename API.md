# Avant de commencer

#### Temps de travail : 2 jours maximum

#### Pré-requis : PHP, Apache, MySQL.

Nous allons utiliser Laravel pour la partie API.

Le framework hérite des composants de Symfony et permet de lancer un projet rapidement sans trop formaliser.

De plus, il est demandé sur le marché de l'emploi et permet de commencer en douceur la transition vers Symfony.

Symfony et Laravel ne sont pas identique et ne sont pas destiné au même projet (bien qu'il soit possible d'utiliser l'un ou l'autre dans tout les cas de figure possible).

Assurez-vous d'avoir Composer d'installé sur votre machine, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM gère les paquets de Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel depuis Composer et créer un nouveau projet :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Une fois l'installation terminé, utiliser votre terminal et déplacer vous dans le nouveau dossier "api". Lancer le serveur web de Laravel, cela nous évitera d'utiliser Apache et nous fera gagner du temps :

```
php artisan serv
```
Passons maintenant à l'étape crucial : la création de la base de donnée depuis phpMyAdmin ou votre terminal. Si vous avez un trou de mémoire :

```sh
mysql -u<username> -p<password>
CREATE DATABASE <dbname>
```

Assurez-vous de donner les droits nécessaires en écriture au dossier "api/storage". Si une erreur de type "MonoLog" se produit plus tard, c'est sûrement car ce dossier n'est pas accessible en écriture.

Editer le fichier "api/.env", coeur de configuration de Laravel, pour y insérer vos informations de connexion MySQL.

Pour finir, je vous invite à télécharger POSTMAN qui vous permettra de tester votre API facilement. Il fonctionne en tant que plugin Chrome : https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop

Enfin, il est temps de tester : http://localhost:8000 devrait vous afficher la page d'accueil par défaut de Laravel.

**Il est inutile de continuer le sujet si vous avez des erreurs ou que la page d'accueil ne s'affiche pas.**

### Etape 1 : Migration

La première étapes va être de modéliser notre base de donnée.

Laravel intègre un outil de migration qui permet de créer votre base de donnée via des classes PHP.

Tout les frameworks moderne intègre cette fonctionnalitées.

Finit les longues minutes sur phpMyAdmin à sélectionner des listes et remplir des cases.

L'avantage de ce système est triple :

- Les autres développeurs pourront voir tout changement sur la base donnée via le code et retracer l'historique des changements.
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback), plutôt pratique si l'on se trompe.
- Les migrations sont partagées lorque vous faîtes un commit, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans demander un dump, juste en executant à leur tour les migrations.

Vous allez devoir réfléchir à la structure de la base de donnée pour 5 tables :

- Users
- Films
- Categories
- Tickets

Certaines tables ont des relations qu'il faudra penser en amont.

Avant de passer à l'étape suivante, il est nécessaire de créer les migrations pour les 5 tables.

Si vous n'avez pas réussis ou qu'une option vous semble étrange, n'hésiter pas à demander de l'aide.

### Etape 3 : Routes

Les routes permettent de mapper une action sur une URL.

Selon le modèle MVC utilisé par Laravel, nos actions se trouvent dans nos controllers.

Actuellement, vous ne posséde qu'une seul route : la page d'accueil par défaut de Laravel.

Par défaut, la configuration des routes est manuel : vous devez lister les différentes URL avec le controller et la méthode à appeler, pas très pratique et plutôt verbeux si vous avez des dizaines de routes.

Heuresement, Laravel intègre un système de ressource qui vous fera gagner du temps : plus besoin de mapper vos URL, le framework s'en occupe automatiquement.

Vous allez devoir créer 4 ressources :

- Users
- Films
- Categories
- Tickets

Chaque ressource devra être lié à propre controlleur : UsersController, FilmsController, CategoriesController, TicketsController. Ces controllers devront être créer via la ligne de commande ; vous le savez, c'est toujours plus rapide.

Parlons maintenant des middlewares. Vous ne connaissez peut-être pas le principe ? Ce sont des portions de code qui sont executé avant que le framework ne vous envoie une réponse.

Cela permet, par exemple, de créer un système de tracage des actions des utilisateurs (logger) en enregistrant l'URL de la page en cours, de vérifier que l'utilisateur ne serais pas bannis via son IP, de démarrer les sessions, ... Les utilisations sont nombreuses.

Par défaut, Laravel intègre plusieurs middlewares, et même des "groupes" de middleware (le groupe "web" contient 5 middlewares par défaut : EncryptCookies, AddQueudCookiesToResponse, StartSession, ShareErrorsFromSession, VerifyCSRFToken).

Rappellez-vous, nous allons utiliser Angular avec notre API, et nous avons séparer notre site en 2 couches : le back sous Laravel, le front sous Angular. Les deux fonctionnent séparément, dans leur propre serveur en total indépendance l'un de l'autre.

Nous allons donc avoir 2 serveurs, ce qui pose un problème fondamental : une ressource ne peut pas être partagé entre 2 domaine.

Ce qui signifie que notre API ne peut pas être appellé depuis un autre domaine que le notre, plutôt génant : c'est ce qu'on appel le "Cross-origin resource sharing" (CORS).

De plus, nous n'avons ni besoin des sessions et des cookies, le groupe de middleware "web" est destiné au site qui tourne entièrement sous Laravel de manière classique.

Pour activer le CORS sur Laravel, il faut installer ce paquet : https://github.com/barryvdh/laravel-cors

Une fois installé, il va falloir appliquer 2 middlewares (API et CORS) à nos routes. Pour cela, crééons un groupe qui contiendra toutes nos routes :

```php
Route::group(['middleware' => ['api', 'cors']], function() {
  // Vos routes ...
});
```

Félicitation, votre API est maintenant accessible depuis l'extérieur !

### Etape 4 : Models

Attaquons maintenant le vif du sujet, la partie modèle.

Pour commencer, il va falloir créer 4 modèles :

- Users
- Films
- Categories
- Tickets

Nos modèles contiendront les propriétés 'fillable', 'hidden', 'timestamps', je vous laisse vous renseigner sur l'utilité de ces propriétées.

Par défaut, nos modèles héritent de pas mal de méthode. 

### Etape 5 : Gestion d'erreur

### Etape 6 : Users et Authentifications

### Etape 7 : Films et Categories

### Etape 8 : Tickets

Félicitation, l'API est terminé. Vous n'avez plus qu'a
Le framework hérite des composants de Symfony et permet de lancer un projet rapidement sans trop formaliser.

De plus, il est demandé sur le marché de l'emploi et permet de commencer en douceur la transition vers Symfony.

Par mon expérience, j'ai tester CakePHP, CodeIgniter, Laravel, Symfony 

Assurez-vous d'avoir Composer d'installé sur votre machine, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM gère les paquets de Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel depuis Composer et créer un nouveau projet :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Une fois l'installation terminé, déplacer le nouveau dossier "api" à la racine de votre serveur web.

Passons maintenant à l'étape crucial : la création de la base de donnée depuis phpMyAdmin ou votre terminal.

```sh
mysql -u<username> -p<password>
CREATE DATABASE <dbname>
```

Assurez-vous de donner les droits nécessaires en écriture au dossier "api/storage".

Editer le fichier "api/.env", coeur de configuration de Laravel, pour y insérer vos informations de connexion MySQL.

Pour finir, je vous invite à télécharger POSTMAN qui vous permettra de tester votre API facilement. Il fonctionne en tant que plugin Chrome : https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop

# Etape 1 : Migration

La première étapes va être de modéliser notre base de donnée.

Laravel intègre un outil de migration qui permet de créer votre base de donnée via des classes PHP.

Tout les frameworks moderne intègre cette fonctionnalitées.

Finit les longues minutes sur phpMyAdmin à sélectionner des listes et remplir des cases.

L'avantage de ce système est triple :

- Les autres développeurs pourront voir tout changement sur la base donnée via le code et retracer l'historique des changements.
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback), plutôt pratique si l'on se trompe.
- Les migrations sont partagées lorque vous faîtes un commit, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans demander un dump, juste en executant à leur tour les migrations.

Vous allez devoir réfléchir à la structure de la base de donnée pour 5 tables :

- Users
- Films
- Categories
- Tickets

Certaines tables ont des relations qu'il faudra penser en amont.

Avant de passer à l'étape suivante, il est nécessaire de créer les migrations pour les 5 tables.

Si vous n'avez pas réussis ou qu'une option vous semble étrange, n'hésiter pas à demander de l'aide.

# Etape 3 : Routes

Les routes permettent de mapper une action sur une URL.

Selon le modèle MVC utilisé par Laravel, nos actions se trouvent dans nos controllers.

Actuellement, vous ne posséde qu'une seul route : la page d'accueil par défaut de Laravel.

Par défaut, la configuration des routes est manuel : vous devez lister les différentes URL avec le controller et la méthode à appeler, pas très pratique et plutôt verbeux si vous avez des dizaines de routes.

Heuresement, Laravel intègre un système de ressource qui vous fera gagner du temps : plus besoin de mapper vos URL, le framework s'en occupe automatiquement.

Vous allez devoir créer 4 ressources :

- Users
- Films
- Categories
- Tickets

Chaque ressource devra être lié à propre controlleur : UsersController, FilmsController, CategoriesController, TicketsController. Ces controllers devront être créer via la ligne de commande ; vous le savez, c'est toujours plus rapide.

Parlons maintenant des middlewares. Vous ne connaissez peut-être pas le principe ? Ce sont des portions de code qui sont executé avant que le framework ne vous envoie une réponse.

Cela permet, par exemple, de créer un système de tracage des actions des utilisateurs (logger) en enregistrant l'URL de la page en cours, de vérifier que l'utilisateur ne serais pas bannis via son IP, de démarrer les sessions, ... Les utilisations sont nombreuses.

Par défaut, Laravel intègre plusieurs middlewares, et même des "groupes" de middleware (le groupe "web" contient 5 middlewares par défaut : EncryptCookies, AddQueudCookiesToResponse, StartSession, ShareErrorsFromSession, VerifyCSRFToken).

Rappellez-vous, nous allons utiliser Angular avec notre API, et nous avons séparer notre site en 2 couches : le back sous Laravel, le front sous Angular. Les deux fonctionnent séparément, dans leur propre serveur en total indépendance l'un de l'autre.

Nous allons donc avoir 2 serveurs, ce qui pose un problème fondamental : une ressource ne peut pas être partagé entre 2 domaine.

Ce qui signifie que notre API ne peut pas être appellé depuis un autre domaine que le notre, plutôt génant : c'est ce qu'on appel le "Cross-origin resource sharing" (CORS).

De plus, nous n'avons ni besoin des sessions et des cookies, le groupe de middleware "web" est destiné au site qui tourne entièrement sous Laravel de manière classique.

Pour activer le CORS sur Laravel, il faut installer ce paquet : https://github.com/barryvdh/laravel-cors

Une fois installé, il va falloir appliquer 2 middlewares (API et CORS) à nos routes. Pour cela, crééons un groupe qui contiendra toutes nos routes :

```php
Route::group(['middleware' => ['api', 'cors']], function() {
  // Vos routes ...
});
```

Félicitation, votre API est maintenant accessible depuis l'extérieur !

### Etape 4 : Models

Attaquons maintenant le vif du sujet, la partie modèle.

Pour commencer, il va falloir créer 4 modèles :

- Users
- Films
- Categories
- Tickets

Nos modèles contiendront les propriétés 'fillable', 'hidden', 'timestamps', je vous laisse vous renseigner sur l'utilité de ces propriétées.

Par défaut, nos modèles héritent de pas mal de méthode. 

### Etape 5 : Gestion d'erreur

### Etape 6 : Users et Authentification

### Etape 7 : Films et Categories

### Etape 8 : Tickets

Félicitation, l'API est terminé. Vous n'avez plus qu'a
