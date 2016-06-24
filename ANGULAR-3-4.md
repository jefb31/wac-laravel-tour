# Avant de commencer

#### Temps de travail : 2 jours maximum

#### Documentation : https://docs.angularjs.org/api

#### Pré-requis : Node.js >= v6.0.2

Dans la seconde partie de ce training, nous allons inspecter Angular 1.5 de fond en comble !

Il y'a beaucoup de nouvelle notion que vous avez sûrement déjà entendu parler ou même vu, mais qu'il faut bien comprendre avant de commencer.

Vous avez l'habitude de créer des site web de façon classique : pages HTML, fichier(s) CSS, fichier(s) JavaScript, jQuery avec des plugins, framework PHP ou code procédural pour gérer le côté serveur selon les cas. Le tout aggrémenté de :

```sh
git add . 
git commit -m "Modif login"
git push origin master
```

Cela ne posais pas vraiment de problème. Vous arrivez à vous organiser de cette manière et c'est très bien. Et jusqu'à récemment, c'est ce que tout le monde faisait. On pensait qu'une page n'était fait que de contenu.

Depuis quelques années, c'est l'effervescence : le web n'a jamais autant bougé ! De nouvelles technologies sorte toutes les semaines et on entend les mots "Gulp", "WebPack", "ES6", "SASS" dans tout les sens.

Il resort plusieurs choses de ces évolutions :

### Les modules

Les modules, c'est à la mode ! Vous en avez obligatoirement utilisé avec Node.js et la fonction "require" qui se charge d'importer un module dans votre propre code. Et vous en installez tout les jours avec NPM, le gestionnaire de paquet de Node.js !

```js
var http = require('http'); // Importe le module 'http' qui se trouve dans le dossier /node_modules/
var express = require('express'); // La même chose pour express
```

Il est possible de créer ses propres modules avec "module.exports" :

```js
module.exports = {
  sayHelloInEnglish: function() {
    return "HELLO";
  },
       
  sayHelloInSpanish: function() {
    return "Hola";
  }
};
```
Et on peut ensuite les importer dans un autre fichier.

Aujourd'hui, on est capable de faire la même chose dans les navigateurs et c'est ce que nous allons voir.

Il y'a quelque semaines, je vous avais présenté un slide sur ECMAScript 6 (aussi appelé ES2015) qui est le nouveau standard de JavaScript. Je vous ai demandé de voir la vidéo de Grafikart de 28mn sur ECMAScript 6, n'hésiter pas à la revoir.

Pas supporté intégralement dans les navigateurs, il existe des compilateurs permettant de transformer votre code ES6 en ES5 (du code JavaScript classique que vous auriez l'habitude d'écrire).

L'ES6 propose un système de chargement de module : oublier le vieux "require" de Node.js, c'est désormais totalement intégré au JavaScript pour peut que vous utilisez un compilateur.

```js
import http from 'http'; // équivalent de var http = require('http');
import { fonctionA, fonctionB } from './monFichier'; // import juste fonctionA et fonctionA depuis monFichier.js
```

Pour exporter, oublier "module.exports", on part directement sur le mot clef "export" qui définit ce qu'on exporte :

```js
export function fonctionA() {
 console.log('Hello');
};

export function fonctionB() {
 console.log('Holla');
};
```

On peut aussi préciser un export par défaut qui sera utilisé quand on n'utilise pas les accolades comme dans l'exemple çi-dessus avec le paquet http qui possède un export par défaut :

```js
function superFonctionExportable() {
  console.log('Hello world !');
}

export default superFonctionExportable;
```

Vous allez pouvoir découper votre code JavaScript en plusieurs fichiers.

Nous allons utiliser ECMAScript 6 dans ce projet et vous retrouverez très souvent cette syntaxe ; j'y reviendrais plus tard.

### Les préprocesseurs

Je crois que depuis que j'ai commencé le développement, je n'ai jamais produit une feuille de style CSS dont j'était fier. Et je pense que vous ressentez la même chose : au bout d'un moment, notre fichier CSS ne ressemble plus à rien, les règles n'ont plus de sens, certaines sont écrasés, des préfixes CSS complique la tache, nous perdons du temps à mettre des signes ": ; { }" totalement inutile qui augmente la taille de notre fichier et rend encore plus illisible le code. En travail d'équipe, c'est encore pire et travailler dans un seul fichier pour tout un site, ce n'est plus possible en 2016.

Heuresement, les préprocesseurs sont arrivés. Leur but ? Vous faciliter la vie en vous faisant coder dans un autre langage puis en transformant ce code en CSS. Le plus connu est SASS dont vous avez sûrement entendu parlé.

Voici un exemple de préprocesseur que j'aime particulièrement, Stylus, qui réduit la syntaxe CSS au minimum :

```stylus
mainBackground = #27332c

body
  background-color mainBackground
  color #fff
  font-family 'Open Sans', sans-serif
  
.search
  margin 20px

  @media all and (max-width 768px)
    margin 10px
```

Il est possible de créer des variables, d'imbriquer des règles (içi, j'ai une règle ".search" avec une media query qui s'applique à l'intérieur), il n'y à plus de signe embarassant ; : { } et on utilise la tabulation pour imbriquer nos règles et décaller les propriétés.

Il est très important d'utiliser correctement la tabulation pour formater votre fichier Stylus sinon le compilateur sera perdu. Chaque décallage représente un niveau, il est possible d'imbriquer à l'infini.

Voici ce que le compilateur va sortir en CSS :

```css
body {
  background-color: #27332c;
  color: #fff;
  font-family: 'Open Sans', sans-serif;
}
.search {
  margin: 20px;
}
@media all and (max-width: 768px) {
  .search {
    margin: 10px;
  }
}
```

Comme vous le voyez, le code Stylus est bien plus lisible et les variables sont réutilisable partout ! Le code en sortie est très propre.

La commande "@import" vous permet d'importer des fichiers Stylus ou CSS dans votre fichier Stylus. Ils seront tout simplement concaténé.

```stylus
@import '../node_modules/bootstrap/css/bootstrap.css'; // importe Boostrap depuis Node.js
@import './mesVariables'; // importe "mesVariables.stylus" avec pleins de variables, couleurs, ...

body
  background-color mainColor // on utilise une variables importé de "mesVariables.stylus"
```

Encore une fois, cette fonctionnalité s'avère très pratique et cela permet de découper ces fichiers CSS en plusieurs morceaux.
A la manière des modules en ES6 qui permettent de découper son code, c'est désormais possible avec un préprocesseur pour le CSS.

Nous utiliseront Stylus dans ce projet pour les quelques ligne de CSS que nous aurons à créer.

### Tasks runner

Un Tasks runner est par définition un outil qui permet d’automatiser les tâches répétitives. C’est une solution simple et agréable dans un certains nombres de cas. L’intérêt est de gagner du temps pour résoudre certaines tâches lors du développement. C’est le genre d’outil qui est particulièrement intéressant dans le cadre de l’industrialisation de vos applications.

Grunt est le plus populaire des Tasks Runners. Historiquement c’est l’un des plus anciens et il dispose d’un nombre de modules important pour résoudre les problématiques. Minification de fichier CSS et JavaScript pour optimiser le temps de chargement, prefixage automatique du CSS des propriétés -webkit-/-moz etc, compilation via des préprocesseurs comme SASS ou Stylus, les modules disponible sont nombreux. Gulp est une autre alternative, très utilisé.

Les taches sont créer dans un fichier JavaScript, on code, on lance une commande dans le terminal et le task runner effectue les taches. On peut enchainer des taches (je prend mon fichier CSS, j'ajoute les préfixes, je minifie, je le renomme, je l'enregistre) et il existe des outils pour que le task runner se relance dès que vous écrivez du code dans votre projet.

Mais aujourd'hui, ces technologies ont été remplacé par un outil appellé "WebPack", le "module bundler".

L'approche de WebPack est plus complexe mais beaucoup plus puissante : plutôt que d'utiliser des taches, le principe est de se dire que n'importe quel fichier est un module. Une image, un fichier HTML, un fichier CSS, tous deviennent des modules JavaScript, pour peut que vous ayez les "loaders" (les plugins WebPack) de chargé dans sa configuration.

Par défaut, WebPack n'est capable de gérer que du JavaScript, mais il possède un système de loaders qui permettent transformer d'autres types de ressources en Javascript. Ainsi, chaque ressource chargée par WebPack devient un module qui sera inclu dans le fichier de sortie à la fin de la compilation :) . Imaginer un fichier "navbar.js" :

```js
require('navbar.styl');

$('.nav').append(require('template.html'));
```

C'est terriblement puissant : comme mon fichier HTML et Stylus sont des modules, je peut l'inclure dans mon JS grâce à l'ES6.

C'est un outil que j'utilise dans tout mes projets front depuis plusieurs mois et je ne peut plus m'en passer.

Avec les solutions existantes, il est actuellement très difficile d'arriver facilement à ce que propose WebPack, pour ne pas dire impossible.

Pour ce projet, j'ai décider de vous créer une configuration WebPack.

Si vous avez du mal à comprendre pourquoi importer son style et son template par un import c'est le bien, retenez juste que WebPack permet de disposer de toutes les ressources statiques (CSS, images, fonts, JS, HTML, ...) en tant que module importable.

La configuration que je vous ai préparé intègre :

- 3 points d'entrée : app.js, app.styl, index.html
- Loader de module : HTML, CSS, JS, Stylus, toutes les images et les fonts.
- Autoprefixage du CSS et Stylus avec -webkit/-moz/etc.
- Linter JavaScript : votre code est analysé selon la norme d'AirBnB pour vous viter les erreurs.
- ECMAScript 6 disponible grâce au loader Babel, vous avez même des morceaux d'ES7 qu'on utilisera.
- Minification et 3 fichiers + 1 dossier en sortie : app.css, app.js, index.html, un dossier assets/ pour les images/fonts.
- Serveur de développement avec hot-reload (rafraichissement automatique quand vous codez).

### ECMAScript 6

Pour finir cette partie, je vais maintenant vous parler de l'ES6. Dans ce projet, nous allons utiliser l'ES6.

ECMAScript 6 apporte une tonne de nouveauté et il serais impossible de vous faire retenir autant d'information. Nous allons donc nous contenter de 28mn minutes de vidéo. Je vous demander de regarder ce tutoriel de Grafikart et de vraiment vous concentrer au maximum car les informations à retenir sont vraiment capitale pour la suite. N'hésité pas à repasser dessus si vous n'avez pas compris un concept.

La vidéo en question : https://youtu.be/5146X8FSBUQ (28mn)

### Angular

Angular, créer par Google, est fondé sur l’extension du langage HTML par de nouvelles balises (directives) et attributs pour aboutir à une définition déclarative des pages web, par opposition à l’utilisation systématique de l’élément div​ et à la définition des interactions (vous savez, $(..).addClass() et l'armée de fonction de jQuery) avec une page web en JavaScript.

Actuellement 2 branches d'Angular existent (v1, v2). La deuxième version permet d'utiliser un framework plus performant, mais nous allons utiliser la première version pour commencer. En suivant les bonnes pratiques d'Angular 1.5 en ES6, passer à la version 2 sera bien plus simple pour vous.

Angular amène avec lui différents concept que nous aborderons au fur et à mesure.

# Installation

Node.js en version 6.0.2 ou supérieur est requis ; si vous avez une version antèrieur, télécharger la nouvelle version sur le site officiel.

Une architecture toute faîtes est déjà présente pour vous facilitez la vie.

Elle inclue WebPack et la configuration que je vous ai décrite avec Angular 1.5 et Materialize.

```sh
git clone https://github.com/kMeillet/angular-boilerplate
```

Installer les dépendances nécessaires avec Node.js :

```sh
cd angular-boilerplate/
npm install
```

***En cas d'erreurs, il est inutile de passer à l'étape suivante.***

# Architecture

Notre architecture est composé de 3 dossiers :

- env : contient notre système de build WebPack.
- src : les sources du projet Netflix en ES6, Stylus, HTML ; c'est la fête.
- dist : les sources une fois compilés par WebPack, ce dossier est le produit de la compilation lisible par le navigateur.

La racine comporte plusieurs fichiers, dont voici les plus importants :

- .editorconfig : permet de partager une configuration général entre différents éditeurs.

- .eslintrc : ESLint est un linter JavaScript. Le linter analyse la qualité du code et si une anomalie est détéctée, bloque le processus de compilation de WebPack. Nous utiliserons la norme AirBnB en ES6 pour ne pas polluer notre code. Le compilateur vous empêchera d'afficher votre site si vous ne respecter pas la norme. Les erreurs sont affichés dans la console et sur votre navigateur. Si vous ne comprenez pas une règle, demandez moi.

- package.json : Référencement de tout les paquets du projet installés avec NPM et information sur le projet.

Quand vous avez installer les paquet avec "npm install", vous avez téléchargé et installé les paquets que j'ai enregistré dans le fichier "package.json".

```js
{
  "name": "Angular-boilerplate",
  "version": "1.0.0",
  "description": "Angular 1.5 in component way",
  "scripts": {
    "update": "npm-check-updates -u && npm update",
    "build": "node ./env/build.js",
    "dev": "node ./env/dev-server.js"
  },
  "author": "Meillet Robin",
  "license": "MIT",
  "devDependencies": {
    "autoprefixer": "^6.3.6",
    "babel-core": "^6.7.4",
    "babel-loader": "^6.2.4",
    "babel-plugin-transform-runtime": "^6.6.0",
    "babel-preset-es2015": "^6.6.0",
    "babel-preset-node6": "^11.0.0",
    "babel-preset-stage-2": "^6.5.0",
    "css-loader": "^0.23.1",
    "eslint": "^2.6.0",
    "eslint-config-airbnb": "^9.0.1",
    "eslint-config-standard": "^5.1.0",
    "eslint-friendly-formatter": "^2.0.4",
    "eslint-loader": "^1.3.0",
    "eslint-plugin-import": "^1.6.1",
    "eslint-plugin-jsx-a11y": "^1.0.4",
    "eslint-plugin-promise": "^1.1.0",
    "eslint-plugin-react": "^5.0.1",
    "eslint-plugin-standard": "^1.3.2",
    "extract-text-webpack-plugin": "^1.0.1",
    "file-loader": "^0.8.5",
    "html-loader": "^0.4.3",
    "html-webpack-plugin": "^2.19.0",
    "ora": "^0.2.1",
    "postcss-loader": "^0.9.1",
    "shelljs": "^0.7.0",
    "style-loader": "^0.13.1",
    "stylus": "^0.54.5",
    "stylus-loader": "^2.0.0",
    "webpack": "^1.12.14",
    "webpack-dev-server": "^1.14.1",
    "webpack-hot-middleware": "^2.10.0",
    "webpack-merge": "^0.13.0"
  },
  "dependencies": {
    "angular": "^1.5.5",
    "angular-materialize": "^0.1.7",
    "angular-ui-router": "^0.3.1",
    "hammerjs": "^2.0.8",
    "jquery": "^2.2.3",
    "materialize-css": "^0.97.6"
  }
}
```

Ce fichier est un JSON, les 3 clées intéressante sont :

- "scripts" : Contient des commandes executable avec "npm run". Nous possédons 3 commandes "npm run update", "npm run build", "npm run dev". La première met à jours les paquets du projet, la seconde compile une fois avec WebPack, la troixième charge le serveur de développement de WebPack.

- "devDependencies" : dépendance de développement de notre Netflix.

- "dependencies" : dépendance front de notre Netflix, que nous allons utiliser et inclure dans notre build (jQuery et Hammer.js sont nécessaire pour que Materialize fonctionne en ES6 car il n'est pas encore 100% compatible).

Lorsque vous installez un paquet et que vous souhaitez l'enregistrer dans le "package.json", il faut renseigner un flag :

```sh
npm install <name> --save-dev // sauvegarde dans devDependencies
npm install <name> --save // sauvegarde dans dependencies
npm i ... /// raccourcis de npm install
```

Nous allons avoir besoin d'Angular Cookies, une extension d'Angular pour gérer les cookies.

Ce paquet existe sous NPM sous le nom "angular-cookies", installer le et enregistrez le en tant que dependencies.

```
npm i angular-cookies --save
```

Ensuite, essayons de builder ce que contient le dossier "src", entrer cette commande :

```sh
npm run build
```

Après un temps d'attente, un dossier "dist" s'est créé.

4 fichiers + 1 dossier sont présent à la racine : 

- Notre index.html, copié à chaque fois dans le dossier "dist" lors de la compilation par WebPack et minifié.
- Un fichier CSS minifié + Javascript minifié.
- Un fichier .htaccess pour faire fonctionner Angular sur Apache que WebPack depuis le dossier "env".
- Le dossier assets contient quand à lui tout nos assets, soit actuellement la police "Roboto" utilisé par Materialize.

Ouvrez maintenant le fichier "src/app.styl" : ce fichier Stylus est notre point d'entrée pour nos styles.

La première ligne inclue Materialize (WebPack concatènera le fichier de Materialize dans le notre).

Une variable est ensuité déclaré. Ensuite, plusieurs style de base sont ajoutés pour rendre la page plus jolie, et une animation qui remue un élément sur la page, est présent à la fin du fichier.

Lorsque WebPack a compilé, il a inclu Materialize, utiliser nos variables, ajouter les préfixes CSS nécessaires aux propriétées, convertit le fichier Stylus en CSS, minifier le résulat, et à sortie tout cela dans le dossier "dist" en tant que "app.css".

Le même processus s'est produit sur "app.js", si vous l'ouvrez, vous remarquez d'emblé un import du fichier "vendor.js".

Içi, nous importons carrément le contenu du fichier, il n'y a pas d'export. Ce fichier "vendor.js" contient nos dépendances, soit actuellement uniquement Materialize.

Ensuite, nous importons l'export par défault du fichier "app.main.js" situé dans le même dossier dans la variable app. Ce fichier contient le coeur de notre application Angular, mais nous y reviendrons plus tard, ne l'ouvrez pas tout de suite.

Nous démarrons ensuite angular sur "window.document", soit la fenêtre qui est affiché dans le navigateur :) .

En second paramètre, nous passons notre variable "app" qui est notre application angular que nous avons importé depuis "app.main.js".

Ouvrez maintenant le fichier "index.html", changer le titre du projet par celui de votre choix.

Lorsque nous développons, nous n'avons pas envie de builder à chaque modification du code. C'est pour cela que le serveur de développement existe. Il recompilera losrsque nous codons dans le dossier "src".

Lancer maintenant votre serveur de développement avec la commande suivante :

```js
npm run dev
```

Après un temps de chargement, allez sur l'URL suivante : http://localhost:3002

Votre application Angular doit s'afficher.

Ouvrez ensuite le fichier "app.styl" et modifier la variable "background", choisissez une couleur de fond.

N'avez-vous pas remarqué quelque chose ? Lorsque vous avez modifié votre variable, WebPack a compilé et a instantanément injecté vos modifications dans la page sans rafraichissement :) .

Après ce tour d'horizon, il est temps d'attaquer le vif du sujet et les choses concrètes.

# Etape 1 : Découverte, inscription et connexion

La première chose à faire est de pouvoir s'inscrire et se connecter.

Vous allez devoir créer un service "AuthService" dans le dossier "service" : copier-coller le fichier "ConfigService" dans un fichier "auth.service.js" et rechercher/remplacer le mot "ConfigService" par "AuthService".

Supprimer les méthodes get() et logger() et profité en pour supprimé l'import et la constante "CONFIG" en haut du fichier.

Supprimer tout le contenu du constructeur, laisser le vide pour l'instant ; cela nous permet d'avoir un service prêt à être codé :) .

Un service permet de créer du code logique dont l'utilisation va se répéter : on aura souvent besoin de se connecter et de se déconnecter et de stocker les informations de l'utilisateur à travers toute notre application. Il peut exister plusieurs moyen de se connecter et on a pas forcément envie de donner coder en dure les fonctionnalités, on préfère faire une classe qui découpe notre logique.

Notre classe sera AuthService et elle possède actuellement une méthode static "factory" qui renverra une nouvelle instance de notre classe.

Factory est un design-pattern très simple qui sert à masquer la complexité de la construction, en facilitant la création d'une nouvelle instance.

Voici un exemple en ES6 :

```js
class Pokemon {
  constructor(name, type) {
    this.name = name;
    this.type = name;
   
    console.log(`Ton nom de pokémon est : ${name} et ton type ${type}`);
  }
}

class Digimon {
  constructor(name, race) {
    this.name = name;
    this.race = race;
   
    console.log(`Ton nom de digimon est : ${name} et ta race ${race}`);
  }
}

class DresseurFactory {
 static factory(type, ...params) { // transforme le reste en tableau
   const [name, secondParametre] = params; // casse le tableau en 2 variables

   if (type === 'POKEMON') {
    return new Pokemon(name, secondParametre]);
   } else {
   return new Digimon(name, secondParametre);
 }
}

const mew = DresseurFactory.factory('POKEMON', 'Mew', 'Psy');
const aegios = DresseurFactory.factory('DIGIMON', 'Angios', 'God');
```

Chaque appel de DresseurFactory.factory renvoie une nouvelle instance de Pokemon ou Digimon.

Certaines fois, les constructions entre les classes peuvent être complexe et le design-pattern factory facilite cela.

Les factory sont nécessaire pour qu'Angular puisse utiliser nos services et injecte les dépendances de notre service (car oui, vous avez le voir, notre service va avoir besoin ... d'autre services).

Si nous souhaitons utiliser un service dans notre service, il va falloir l'injecter. 

$inject est un service d'Angular qui se charge d'injecter les dépendances de notre service.

Il s'utilise de la manière suivante, à la fin d'une classe :

```js
AuthService.factory.$inject = [];
```

Ajouter cette ligne à la fin de votre AuthService.

Anguler ne créé le service une fois, la première fois qu'il est appelé.

Ensuite, c'est un singleton (instance unique) : il ne rappellera jamais la fonction factory.

Angular possède bon nombre de service comme $http pour faire des requêtes AJAX, $state pour rediriger, et $inject pour injecter des dépendances.

Nous allons avoir besoin du service $http, injectons-le :

```js
AuthService.factory.$inject = ['$http'];
```

Ensuite, ajoutons l'argument dans notre factory :

```js
/**
 * Factory of this class
 * @returns {AuthService}
 */
static factory($http) {
  return new AuthService($http);
}
```

Et enfin, dans le constructeur de notre service :

```js
constructor($http) {
  
}
```

Désormais, il va falloir enregistrer le service lorsque le constructor est lancé. Pour cela, créer une constante SERVICES à la ligne 1, qui sera accessible partout dans ce fichier.

```js
const SERVICES = new Map();
```

Une Map est un nouveau type en ES6 : c'est une paire de clef => valeur, comme peuvent l'être les tableaux en PHP ou les hash en Ruby.

Une Map possède les méthodes "get", "set", "has", "destroy", "keys", "values", ... et la propriété "size".

Documentation : https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Map

C'est un moyen très simple de stocker des données, voici un exemple :

```js
const SERVICES = new Map();

SERVICES.set('clef', 'valeur');
SERVICES.get('clef'); // => valeur
SERVICES.has('yolo') // => false
SERVICES.size // => 1
SERVICES.delete('clef')
SERVICES.size // => 0
```

Notre constante nous servira à enregistrer tout les services que notre service utilise.

Ajoutons le service $http :

```js
constructor($http) {
  SERVICES.set('$http', $http);
}
```

Désormais, le service $http est accessible dans toute notre classe via :

```js
SERVICES.get('$http');
```

Rien de plus simple ! Si vous souhaitez utiliser un service dans un service, il faudra se rappeler de la procédure : ajout dans $inject, ajout dans la fonction factory, ajout dans le constructeur, et stockage dans une Map :) .

Maintenant, nous allons devoir créer notre formulaire.

Pour décomposer notre application, nous utilisons les composants : cela permet de découper l'application en plusieurs morceaux.

Un composant est une balise HTML (exemple : <searchbar></searchbar>) qu'Angular va reconnaître et utiliser.

Un composant possède un controller et un template. WebPack permet d'ajouter du style.

Les composants sont isolés les un des autres et une bonne pratique est d'isoler leur état : plus votre composant intéragis avec l'extérieur, plus il est difficile à tester.

Si vous ouvrez le dossier "src/component", vous verrez plusieurs composants :

- Netflix-Home : correspond au composant de la page d'accueil.
- Netflix-Login : correspondant au composant de la page d'authentification

Ils existent 2 types de composant :

- Les composants parent : Netflix-home et Netflix-login le sont, ce sont des composants qui sont les "parent" de composant enfants.

- Les composants enfants : ce sont des composants qui ont un parent et qui ne peuvent pas fonctionner seul.

Ouvrez le fichier "src/constant/route.js". C'est içi que nous définissons toutes nos routes pour Angular.

Remarquez vous les balises dans le mot clef "template" ? Cela correspondant à nos composants Netflix-Home et Netflix-Login.

Lorsque nous accédons à la page d'accueil, le composant Netflix-Home est chargé et son controlleur est appelé immédiatement. Ouvrez le controller du Netflix-Home.

Remarquez à nouveau l'utilisation de $inject. Sauf que cette fois, il n'y a pas besoin de factory :) .

Le service $state permet de gérer l'utilisation du router pour faire des redirections par exemple.

Si vous inspectez le dossier "src/component/netflix-home/", vous verrez qu'il contient bien un template HTML, un style Stylus, un controller Angular, et un fichier principal qui relie ces 3 modules en un seul, exporté.

Le point d'entrée de tout les composants est le fichier "src/app.component.js". C'est içi qu'on enregistre chaque composant dans la fonctona angular.component !

Il est vital d'importer chaque nouveau composant et de les enregistrer pour qu'Angular puisse s'en servir, on peut chaîner la fonction avec le point en sautant des lignes.

Le première paramètre correspond au nom de la balise : netflixHome donnera netflix-home pour que ce soit "HTML Compatible". Les majuscule sont interprété comme un changement, ce qui donne le tiret séparateur :) . On utilisera toujours un préfix comme netflix actuellement pour éviter les colisions avec d'autre scripts ou une balise HTML existante.

Angular préfixe ces composants et directives avec le mot clef "ng" : ng-repeat, ng-show, ng-model, ng-click, ... Ionic Framework préfix avec "ion" : ion-nav-view, ...

Le même fichier, "src/app.service.js", référence chaque service. A ce propos, il est nécessaire que vous modifiez ce fichier pour ajouter l'AUthService :) .

Le premier paramètre est le nom du service que vous allez pouvoir utiliser, le second paramètre prend la fonction factory de la classe AuthService. N'oubliez pas d'importer le fichier, bien évidemment.

Maintenant que vous commencez à comprendre comment fonctionne notre structure, nous allons pouvoir modifier notre premier composant.

Fermez tout les fichiers de votre éditeur, et ouvrez "src/app.main.js".

C'est içi qu'Angular importe nos routes, nos services, nos composants, ...

Importer 'angular-cookies' :

```js
import cookies from 'angular-cookies';
```

Puis, ajouter cookies au dépendance de notre application Angular ligne 13 :

```js
[cookies, router, materialize, services, components]
```

Maintenant que nous avons Angular Cookies, fermez ce fichier et ouvrez "src/component/netflix-login/template/netflix-login.html".

Votre première tâche est de créer un formulaire de connexion avec login et mot de passe.

Ouvrez ensuite le controller de votre composant, et ajouter un objet "loginForm" dans votre constructeur.

Pour que cette objet soit accessible partout dans notre classe, nous avons besoin d'utiliser "this" :

```js
  constructor($state) {
    SERVICES.set('$state', $state);

    this.loginForm = {};
  }
```

Donnons maintenant des propriétés à notre objet :

```js
  constructor($state) {
    SERVICES.set('$state', $state);

    this.loginForm = {
      login: '',
      password: '',
    };
  }
```

Par défaut, les valeurs seront vide. Vous l'aurez déviné, cela représentre notre formulaire de connexion.

Nous allons utiliser le data-binding, un concept clé d'Angular.

Lorsque nous tapons dans notre formlaire, nous souhaitons que notre objet se mettent à jour. Angular permet cela avec la directive "ng-model". C'est un attribut qui prend en paramètre un modèle pour Angular.

```html
<form>
  <p>Login :</p>
  <input type="text" ng-model="vm.loginForm.login">
  ...
</form>
```

"vm" représentre le contexte de notre controlleur.

Vous aviez peut-être l'habitude d'utiliser "$scope" qui est un service Angular, utilisé "vm" et stocker les modèles dans "this" est une meilleur pratique.

Utiliser "ng-model" sur le champ login et mot de passe avec le binding correspondant.

Ensuite, il nous faut envoyer le formulaire. Angular intègre la directive "ng-submit" qui se place sur une balise "form". Elle prend en paramètre une fonction :

```html
<form ng-submit="vm.postLoginForm()">
```

Utiliser "ng-submit" et créer une méthode postLoginForm() dans votre controller.

Cette méthode devra console.log votre objet loginForm.

Si vous postez le formulaire, vous devez voir votre objet à jour dans votre console.

***Si ce n'est pas le cas ou que vous avez des erreurs sur WebPack/votre navigateur, venez me voir***

Vous avez bindé votre modèle loginForm avec ng-model, ng-submit lance la fonction postLoginForm().

Il est temps de se connecter.

Ré-ouvrez votre API et créer une route en POST sur '/auth' qui appel le controller AuthController et la méthode authenticate().
Cette méthode devra effectuer ceci :

```php
$user = User::where('login', '=', Input::get('login'))->firstOrFail();

if (Hash::check(Input::get('password'), $user->password)) {
  return Response::json($user, 200, [], JSON_NUMERIC_CHECK);
}

return Response::json(['errors' => 'Invalid credentials.'], 401, [], JSON_NUMERIC_CHECK);
```

Penser à importer les namespaces nécessaires.

Modifier votre UserController et ajouter la fonction bycrypt() sur le champ password envoyé dans store() et update().

Cela permettra de sécurisé le mot de passe via un cryptage.

Créer un fake-user avec POST-MAN, noter le login et password dans un coin.

Votre route '/auth' est censé fonctionner avec POST-MAN.

Fermer votre API et réouvrez votre projet Angular.

Vous allez devoir utiliser le service AuthService, ouvrez le fichier et ajouter une méthode tryToAuthenticate.

Elle prendra un paramètre, "credentials" qui sera un objet avec un couple login/password.

Cette méthode devra retourner la méthode "post" du service $http qui prend 2 paramètres :

- L'URL de la requête (http://localhost/auth par exemple)
- Un objet correspondant aux données envoyée 

Utiliser l'URL de votre API vers l'authentification et envoyer le paramètre de notre fonction :

```js
return SERVICES
        .get('$http')
        .post('http://localhost/auth', credentials);
```

Une fois la requête envoyé, il nous faut une réponse, utilisons le système de promesse d'Angular :

```js
return SERVICES
        .get('$http')
        .post('http://localhost/auth', credentials);
        .then(data => console.log('Réussis', data))
        .catch(error => console.log('Fail', error));
```

Then est attendu lorsque l'opération réussis. Catch se produit lorsque sa fail.

Il est possible d'utiliser plusieurs then/catch, et c'est ce que nous allons faire, vous prendrez l'habitude :) .

Maintenant que notre méthode est codé, il nous faut stocker les informations de l'user. Lorsque cette fonction sera appellé par notre controller du composant Netflix-Login, il faut que si les informations sont correct, stocker les données et enregistrer les informations dans un cookies.

Ajouter dans votre constructeur d'AuthService :

```js
this.user = false;
```

La première fois que nous appelons le service, l'user est vide. Lorsque la fonction tryToAuthenticate est appelé, nous devons modifier cette variable :

```js
return SERVICES
        .get('$http')
        .post('http://localhost/auth', credentials);
        .then(data => {
            this.user = data;

            console.log('Valid credentials', data);

            return data;
         })
        .catch(error => console.error('Invalid credentials !', error));
```

Nous retournons les données pour qu'après la connexion, notre controller puisse continuer à utiliser then et rediriger l'utilisateur.

Utilisons maintenant notre méthode dans le controller de Netflix-Login.

Injecter votre service d'authentification et modifier votre méthode postLoginForm :

```js
postLoginForm() {
 SERVICES.get('authService')
         .tryToAuthenticate(this.loginForm)
         .then(() => SERVICES.get('$state').transitionTo('home'));
}
```

Puisque notre méthode tryToAuthenticate retourne notre promesse, nous pouvons continuer à utiliser un deuxième then() qui sera lancé si la fonction réussis :) .

Notre code est clair et lisible de haut en bas, lorsque nous avons réussis, nous redirigons vers la page d'accueil.

Essayez maintenant de vous connecter, il est probable que vous ayez rater une information. En cas de problème, n'hésitez pas à venir me voir.

Vous êtes maintenant capable d'utiliser le service $http d'Angular pour utiliser voter API.

La prochaine étape est l'inscription, créer un composant "Netflix-Register" en vous inspirant du "Netflix-Login", enregistrez la route, et faîtes des tests.

Pour faire des lien entre vos routes, utiliser la directive "ui-sref" en attribut avec le nom de la route, Angular se chargera de créer un lien vers la route :

```html
<a ui-sref="home">Accueil</a>
```

Après l'inscription, il faudra gérér les permissions. Vous devrez faire en sortes que les utilisateurs ne puissent pas visualiser la page d'accueil et soit automatiquement rediriger vers la route '/auth' de votre site si la variable 'user' de votre AuthService est égal à "false".

Vous devrez aussi ajouter le système de cookies pour sauvegarder les informations de l'utilisateur à la connexion, et modifier le constructeur de votre AuthService pour vérifier que le cookies est présent, et le cas échéant modifier ls informations et connecter automatiquement l'utilisateur plutôt que de mettre la valeur à "false".

La prochaine étape sera disponible Vendredi à 12h00. Nous ajouterons la liste des films, le détail d'un film et des catégories, puis nous ajouterons une recherche, et l'ajout de ticket.

**Note :**

Si vous obtenez une erreur Cross-Origin-Request dans votre console, il sera nécessaire d'utiliser Apache plutôt que le serveur de Laravel.

N'oubliez pas d'éditer votre fichier ".env" dans votre API pour changer l'URL de votre API si vous le changez de place.
