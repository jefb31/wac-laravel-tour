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
Et on peut ensuite les importer dans un autre fichier. Très pratique vous me direz car dans le navigateur, nous n'avons pas l'habitude de morceler notre code.

Aujourd'hui, on est capable de faire la même chose dans les navigateurs et c'est ce que nous allons voir.

Il y'a quelque semaines, je vous avais présenté un slide sur ECMAScript 6 (aussi appelé ES2015) qui est le nouveau standard de JavaScript.

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

La suite sera disponible Jeudi à 11h00.
