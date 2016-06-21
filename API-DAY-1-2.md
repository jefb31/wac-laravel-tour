# Avant de commencer

#### Temps de travail : 2 jours maximum

#### Documentation : https://laravel.com/docs/5.2

#### Pré-requis : PHP, MySQL

Nous allons utiliser Laravel pour la partie API.

Le framework utilise des composants de Symfony et permet de lancer un projet rapidement sans trop formaliser.

De plus, il est demandé sur le marché de l'emploi et son système de façade rend son utilisation simple et accessible.

Symfony et Laravel ne sont pas identique et ne sont pas destiné au même projet (bien qu'il soit théoriquement possible d'utiliser l'un ou l'autre dans tout les cas de figure, on préfère utiliser la meilleur solution au problème plutôt que le meilleur problème face à la solution).

Assurez-vous d'avoir Composer d'installé sur votre machine, si ce n'est pas le cas : https://getcomposer.org

Composer est le gestionnaire de paquet principal de PHP, au même titre que NPM gère les paquets de Node.js.

Lancer un terminal et utiliser cette commande pour télécharger Laravel et créer un nouveau projet depuis Composer :

```sh
composer create-project --prefer-dist laravel/laravel api
```

Vous le savez sûrement, Laravel intègre des outils en ligne de commande qui vous feront **toujours** gagner du temps.

Une fois l'installation terminé, utiliser votre terminal et déplacez-vous dans le nouveau dossier "api".

Lancer le serveur web de Laravel, cela nous évitera d'utiliser Apache :

```php
php artisan serv
```

Passons maintenant à l'étape crucial : la création de la base de donnée depuis phpMyAdmin ou votre terminal.

```sh
mysql -u<username> -p<password>
CREATE DATABASE <dbname>
```

Assurez-vous ensuite de donner les droits nécessaires en écriture au dossier "api/storage" : si une erreur de type "Monolog" se produit, c'est sûrement car ce dossier n'est pas accessible en écriture.

Editer le fichier "api/.env", coeur de configuration de Laravel, pour y insérer vos informations de connexion MySQL.

Pour finir, je vous invite à télécharger POSTMAN qui vous permettra de tester votre API facilement via une interface graphique.

Il fonctionne en tant que plugin Chrome : https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop

Parfait, il est temps de tester votre projet : http://localhost:8000 doit vous afficher la page d'accueil par défaut de Laravel.

![Laravel](https://mattstauffer.co/assets/images/content/Screen-Shot-2015-01-13-at-11.42.04-PM.png)

**Il est inutile de continuer le sujet si vous avez des erreurs ou que la page d'accueil ne s'affiche pas.**

### Etape 1 : Migrations

La première étapes va être de modéliser notre base de donnée.

Laravel intègre un outil de migration qui permet de créer votre base de donnée via des classes PHP.

Tout les frameworks moderne intègre cette fonctionnalitées et il est indispensable de savoir comment l'utiliser.

L'avantage de ce système est triple :

- Les autres développeurs pourront voir tout changement sur la base donnée via le code et retracer l'historique des changements.
- Il est possible d'executer une migration (migrate) ou de revenir en arrière (rollback), plutôt pratique si l'on se trompe.
- Les migrations sont partagées lorque vous faîtes un commit, ce qui signifie que les autres développeurs pourront prendre en compte vos modifications sans demander un dump de votre base de donnée, juste en executant à leur tour les migrations.

Nous allons utiliser 4 tables pour notre projet :

- Users
- Films
- Categories
- Tickets

Pour créer une migration, utiliser la commande suivante :

```
php artisan make:migration create_<name>_table
```

Votre migration vient d'être créé dans le dossier "database/migrations".

Vous n'avez qu'à remplir les méthodes up() et down(), correspondant respectivement à la migration et au rollback.

Pour partir sur une base commune, je vous propose de reproduire les tables suivantes : https://github.com/kMeillet/wac-laravel-tour/blob/master/DATABASE.md

Penser à utiliser les options "softDelete" pour ne pas réellement supprimer un résultat et "timestamp" qui créera automatiquement les champs "created_at" et "updated_at" dans votre table.

**Vous ne devez pas changer les noms des champs et respecter à la lettre les contraintes, respecter bien cette étapé.**

Une fois les 4 migrations créé et complété, executer la commande çi-dessous, et passer à l'étape suivante.

```
php artisan migrate
```

### Etape 2 : Routes

Les routes permettent de mapper une action sur une URL.

Selon le modèle MVC utilisé par Laravel, nos actions se trouvent dans nos controllers.

Actuellement, vous ne posséde qu'une seul route : la page d'accueil par défaut de Laravel.

Par défaut, la configuration des routes est manuel : vous devez lister les différentes URL avec le controller et la méthode à appeler, pas très pratique et plutôt verbeux si vous avez des dizaines de routes.

Heuresement, Laravel intègre un système de ressource qui vous fera gagner du temps : plus besoin de mapper vos URL, le framework s'en occupe automatiquement.

```php
Route::resource('photos', 'PhotosController'); // Un exemple de ressource
```

Dans cette exemple, voici ce que Laravel va créer automatiquement pour vous :

![Ressource](http://image.noelshack.com/fichiers/2016/25/1466387953-legend-restful-res.png)

La troixième colonne correspond à la méthode appelé dans le controller.

Vous allez devoir créer 4 ressources :

- Users
- Films
- Categories
- Tickets

Chaque ressource devra être lié à son propre controlleur : UsersController, FilmsController, CategoriesController, TicketsController. Ces controllers devront être créer via la ligne de commande ; vous le savez, c'est toujours plus rapide.

```php
php artisan make:controller Users --resource
```

Parlons maintenant des middlewares.

Vous ne connaissez peut-être pas le principe ? Ce sont des portions de code qui sont executé avant que le framework ne vous envoie une réponse.

Cela permet, par exemple, de créer un système de tracage des actions des utilisateurs (logger), de démarrer les sessions, ... Les utilisations sont nombreuses.

Par défaut, Laravel intègre plusieurs middlewares, et même des "groupes" de middleware (le groupe "web" contient 5 middlewares : EncryptCookies, AddQueudCookiesToResponse, StartSession, ShareErrorsFromSession, VerifyCSRFToken).

Rappellez-vous, nous allons utiliser Angular avec notre API, et nous avons séparer notre site en 2 couches : le back sous Laravel, le front sous Angular. Les deux fonctionnent séparément en total indépendance.

Le problème, c'est qu'une ressource ne peut pas être partagé entre 2 domaines.

Nous allons devoir autoriser nos requêtes vers l'API depuis les autres domaines, c'est ce qu'on appel le "Cross-origin resource sharing" (CORS).

Pour activer le CORS sur Laravel, il faut installer ce paquet : https://github.com/barryvdh/laravel-cors

Une fois installé, il va falloir appliquer 2 middlewares (API et CORS) à nos routes.

Pour cela, créons un groupe qui contiendra toutes nos ressources :

```php
Route::group(['middleware' => ['api', 'cors']], function() {
  // Vos ressources ...
});
```

Le groupe de middleware "web" est destiné au site qui tourne sous Laravel de manière classique. Notre API n'utilisera ni cookies ni session, nous n'en n'avons donc pas besoin. Il existe un groupe de middleware "api" prévu pour notre cas.

Tester quelques routes, une page blanche doit s'afficher car nos controlleurs sont vides.

### Etape 3 : Modèles

Attaquons maintenant le vif du sujet, la partie modèle.

Pour commencer, il va falloir créer 4 modèles, chacun correspondant à une table au singulier :

- User
- Film
- Category
- Ticket

Encore une fois, la ligne de commande vous permettra de créer vos modèles très rapidement :

```php
php artisan make:model User
```

Par défaut, les modèles héritent de tout un tas de méthode utiles.

Nos modèles contiendront les propriétées 'fillable', 'hidden', 'timestamps', je vous laisse vous renseigner sur l'utilité de ces propriétées. Il faudra utiliser le trait "softDelete" dans votre modèle pour activer le softDelete.

Il va falloir définir nos relations entre chaque modèle. Laravel intègre plusieurs type de relation (hasOne, hasMany, belongsTo, ...). Pour s'aider, il suffit de parler en anglais : "Users has many tickets, a ticket belongs to user".

La norme souhaite que l'on nomme la relation avec le nom du modèle correspondant, exemple avec le modèle ticket :

```php
// Le modèle Ticket possède 2 relations car un ticket appartient à 1 User et 1 Film

public function user()
{
    return $this->belongsTo('App\User');
}

public function film()
{
    return $this->belongsTo('App\Film');
}
```

Créer les 4 modèles avec les relations correspondantes avant de passer à l'étape suivante.

Pour tester votre modèle, vous pouvez utiliser la REPL de Laravel :

```php
php artisan tinker
```

### Etape 4 : Gestion d'erreur

Un problème particulier se pose : nous n'avons aucune gestion d'erreur.

Imaginons qu'un controlleur ai besoin du modèle User pour rechercher l'utilisateur avec l'ID 15 et que cette utilisateur n'existe pas. Notre API devra renvoyer un message d'erreur.

Laravel intègre une gestion d'erreurs avec les exceptions. Voci une liste des exceptions présente de base :

- AuthorizationException : problème d'authentification.
- HttpException : problème de routing (404, ...).
- ModelNotFoundException : notre modèle n'existe pas.
- ValidationException : la validation de formulaire n'a pas réussis.

Lorsque nous utilisons une API, nous souhaitons prendre le contrôle sur les erreurs. Et plus particulièrement sur MoedlNotFoundException qui lève une exception lorsque notre modèle n'existe pas. Cela nous évitera de coder des vérification dans tout les modèles.

Plutôt que d'afficher les erreurs en brut, ce qui n'aurait pas d'intérêt pour une API, nous allons capturer les erreurs et les renvoyer au format JSON.

Ouvrer le fichier "app/Exceptions/Handler.php" et remplacer la méthode render() par celle-ci :

```php
/**
 * Render an exception into an HTTP response.
 *
 * @param  \Illuminate\Http\Request $request
 * @param  \Exception $e
 * @return \Illuminate\Http\Response
 */
public function render($request, Exception $e)
{
    if ($e instanceof ModelNotFoundException) {
        return Response::json(['errors' => 'Not found.'], 404);
    }

    return parent::render($request, $e);
}
```

Désormais, lorsque nous appellons un modèle inexistant (essayer sur http://localhost:8000/users/15), nous obtenons une erreur au format JSON, c'est ce que nous souhaitons.

Ils ne nous restent plus qu'a créer nos controllers pour terminer notre API.

### Etape 5 : Controllers : Users

Lorsque vous avez utiliser la ligne de commande pour créer vos controlleurs, Laravel a automatiquement créer les méthodes correspondant aux routes de la ressource.

Dans notre cas de figure, nous n'avons besoin que des actions CRUD (Create, Read, Update, Delete) :

- Voir les users (méthode index())
- Voir un user en particulier (méthode show())
- Créer un user (méthode store())
- Supprimer un user (méthode destroy())
- Editer un user (méthode update())

Vous pouvez donc supprimer les méthodes create() et edit() qui ne servent qu'à afficher les formulaires de création et d'édition de la ressource, ce qui dans le cas d'une API n'a aucun intérêt.

Voici un exemple de la méthode index() :

```php
/**
 * GET /user
 * @return mixed
 */
public function index()
{
    $users = User::all();

    return Response::json($users, 200, [], JSON_NUMERIC_CHECK);
}
```

Dans cette exemple, je charge tout les Users, et je renvoi une réponse au format JSON avec un code 200. Le dernier paramètre indique que nous ne souhaitons pas transformer les valeurs numériques en chaîne de caractères, c'est un paramètre que vous devez utiliser à chaque fois que vous envoyer une réponse pour éviter les problèmes de typage.

Le code 200 indique que tout c'est bien passé ; en cas d'erreur de validation d'un formulaire, nous aurions pu renvoyer un code 422 stipulant que le formulaire n'a pas été correctement remplit.

Voici une liste des codes HTTP que vous retrouverez très souvent :

- 200 : succès de la requête.
- 301 et 302 : redirection, respectivement permanente et temporaire.
- 403 : accès refusé.
- 404 : page non trouvée.
- 422 : formulaire invalide.
- 500 et 503 : erreur serveur.

**La réponse de votre API sera toujours sous ce format, seul les 2 premiers paramètres de Response::json() seront changées en fonction des circonstances. Les 2 derniers paramètres ne changeront jamais, prenez l'habitude de les inclure à chaque fois lorsque vous renvoyez des données dont vous n'avez pas connaissance des résultats.**

Vous allez maintenant devoir compléter les 5 méthodes pour pouvoir faire les actions CRUD via votre API.

Vous n'avez pas besoin de vérifier que l'User existe pour les méthodes update() et show(), car le système d'exception de Laravel que nous avons configuré précédemment gère déjà les modèles inexistant en renvoyant une erreur.

En revanche, il est souhaitable de créer une validation concernant les méthodes update() et store() : nous ne voulons pas que quelqu'un puisse rentrer des informations invalide.

La façade "Validator" permet de créer une validation avec des règles prédéfinis. Utiliser bien les contraintes de la table du modèle (le champ est-il unique ? est-il optionnel ? est-ce un nombre ? une email ?) pour créer une validation performante. En cas de formulaire invalide, vous afficherez les erreurs avec un code 422.

Concernant la récupération des variables GET et POST, la façade "Input" permet cela : Input::all() renvoi toutes les données tandis que Input::get('key') renvoi une clef spécifique ou NULL si le champ n'existe pas.

Enfin, une fois que votre controlleur est terminé, vous devez être en mesure de pouvoir faire toutes les actions CRUD sur la ressource Users : utiliser POSTMAN pour tester votre API.

Assurez-vous que votre API se comporte bien avant de passer aux prochains controlleurs.

### Etape 6 : Controllers : Films et Categories

Vous allez compléter les controllers Films et Categories. Rien de difficile à surmonter, tester et inspirer vous de ce que vous avez fait précédement.

Penser à vérifier que vous ne pouvez pas créer un film avec une catégorie inexistante, que l'URL de la photo est bonne (si l'URL est précisé, je vous rappel que c'est un champ optionnel).

### Etape 7 : Controllers : Tickets

A ce stade, les tickets ne devrait pas vous poser de problème et vous devriez être en mesure de remplir le controlleur très rapidement.

Vous n'êtes pas obliger de garder les méthodes update() et destroy() car nous n'aurons jamais besoin de supprimer ou d'éditer un ticket.

L'API est terminé ! Enfin presque, il faut maintenant la tester.

### Avant de passer à l'étape suivante

Avant de passer à l'étape suivante, il est nécessaire de valider votre API : cela se fera Mardi 21 Juin à 13h30.

La piscine MEAN ayant 5 notes sur 5 jours, il faut égaliser. On part sur 2 notes pour ces 2 premiers jours.

Chaque sous-catégorie de la journée est testé, si un fail se présente, on passe à la sous-catégorie suivante, jusqu'à la fin.

### 1) Jours #1 : 20 Points

On va regarder le code et uniquement le code, pas le fonctionnement de l'API.

##### Migrations : 4 Points

- Toutes les migrations sont présente : 0,5
- Les contraintes, type et nom des champs sont respectés en intégralité : 2
- Le softDelete et timestamp sont bien utilisés dans toutes les migrations : 0,5
- Le rollback fonctionne et on peut migrer correctement : 1

##### Routes : 3 Points

- Les ressources RESTFUL avec les verbes HTTP GET/POST/PUT/DELETE sont utilisés : 0,5
- Toutes les routes sont présente et aucune erreur 404 sur les routes demandées n'apparaît : 1,5
- Les middlewares "api" et "cors" sont bien utilisés et installés : 1

##### Modèles : 4 Points

- Tout les modèles sont présent : 0,5
- Les modèles possède des relations et elles sont correctement implémentés : 2
- Les modèles implémente tous les propriétés protégés fillable, hidden et timestamp : 0,5
- Le trait softDelete est correctement utilisé sur tout les modèles : 1

##### Controllers : 9 Points

- Tout les controllers sont présent : 0,5
- Toutes les méthodes de nos ressources sont présentes : 0,5
- Les méthodes inutilisés ne sont plus présentes : 0,5
- Les méthodes effectue les actions CRUD demandés : 3
- La validation existe et les règles sont correctement implémenté : 3
- Il y'a une différence notable entre la validation de la méthode store et update : 1
- Les codes HTTP sont bien utilisés : 0,5 

### 2) Jours #2 : 20 Points

Pour avoir tout les points, assurez-vous que votre API fonctionne bien et que les laisons renvoie bien les données.

Les films doivent renvoyer leur catégorie lié, sur toutes les méthodes.

Les catégories doivent à l'inverse renvoyer la liste de tout leurs films lié ***seulement si on demande la méthode show() pour éviter toute surcharge !***

Les users doivent renvoyer la liste de leur tickets ***seulement si on demande la méthode show() pour éviter toute surcharge !***

Les tickets doivent à l'inverse renvoyer les information de l'user lié ainsi que le film correspondant ***seulement si on demande la méthode show() pour éviter toute surcharge !***

Chercher sur la documentation, c'est très simple à mettre en place !

##### Tests : 15 Points

- On peut sélectionner (all/single), créer, modifier et supprimer un user : 3
- La ressource user a bien les données liés : 1
- On peut sélectionner (all/single), créer, modifier et supprimer un film : 3
- La ressource film a bien les données liés, sur toutes les méthodes : 1
- On peut sélectionner (all/single), créer, modifier et supprimer une catégorie : 3
- La ressource category a bien les données liés : 1
- On peut sélectionner (all/single), créer, modifier et supprimer un ticket : 2
- La ressource ticket a bien les données liés : 1

##### Validations et gestion des ressources introuvables : 5 Points

- La validation des users est fonctionnel : 2
- La validation des films est fonctionnel : 1
- La validation des catégories est fonctionnel : 0,5
- La validation des tickets est fonctionnel : 0,5
- Lorsqu'une ressource est introuvable, une erreur 404 avec réponse JSON est renvoyée : 1

### Points bonus

- Qualité du code, pas d'incohérence : 1
- Pas d'erreur PHP : 1
- Small controller, validation directement dans le dossier "app/Http/Request" avec "artisan make:request" : 1
