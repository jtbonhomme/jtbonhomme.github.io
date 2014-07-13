---
layout: post
title: "Grunt: l'automatisation facile (part 1)"
date: 2014-07-05 11:03:24 +0200
comments: true
categories: grunt, javascript
---

![](http://gruntjs.com/img/grunt-logo.svg)

La majorité des développeurs que je connais ont un point commun, une caractéristique qui les distinguent des autres professions.

Ils sont paresseux.

Loin d'être un défaut, cette paresse est encouragée car elle se traduit par une tendance à éviter les tâches répétitives et fasitidieuses, et libèrent du temps pour les activités plus productives (écriture de documentation et test ... quoique ...)

Les outils qui permettent au développeur consciencieux d'en faire le moins possible foisonnent; générateurs de templates et boilerplates, snippets, gist et autres pastebin, et outils d'automatisation.

C'est à cette dernière catégorie d'outils que nous allons nous intéresser et plus particulièrement au plus célèbre d'entre eux: [grunt](http://gruntjs.com/).

> tous les codes source de cet article sont disponibles sous [github](https://github.com/jtbonhomme/grunt-article)

# A quoi ça sert ?

Grunt est un exécuteur de tâches qui peuvent être lancées unitairement, ou par groupes.

Pour parler concrêtement, le travail quotidien d'un dev front consiste quasi continuellement à :

* développer ou modifier son code (ou celui du voisin)
* corriger les fautes de syntaxes qui ne correspondent pas aux règles de codage
* vider les répertoires de sortie où sont compilés ses fichiers
* compiler ses templates pour générer les fichiers html
* compiler et minifier ses fichiers html
* lancer ses tests unitaires (si si si, il paraît que ça se fait beaucoup)
* déployer son application
* rafraîchir son navigateur

Et recommencer ...

Grunt permet d'automatiser la plupart de ces tâches (non, il n'écrira pas le code ou la doc pour vous, mais il peut néanmoins générer la documentation yuidoc par exemple)

Si on ajoute à ça une communauté active qui propose un grand nombre de plugins pour vous faciliter la vie, pourquoi hésiter plus longtemps ?

# Par quoi on commence ?

> PS: toutes les manipulations ci-dessous tournent sous MAC, avec nodejs et npm installés

Il faut d'abord installer grunt et l'outil de ligne de commande grunt-cli

```
% npm install -g grunt-cli
```
Si à cette étape vous vous amusez à exécuter la commande grunt (ben, faites le, histoire de suivre un peu), vous risquez de voir ceci apparaître :

```
% grunt
grunt-cli: The grunt command line interface. (v0.1.9)

Fatal error: Unable to find local grunt.

If you're seeing this message, either a Gruntfile wasn't found or grunt
hasn't been installed locally to your project. For more information about
installing and configuring grunt, please see the Getting Started guide:

http://gruntjs.com/getting-started
```

Ce message indique que le moteur de grunt n'est pas installé, et que grunt utilise un fichier de configuration <code>Grunfile.js</code> décrivant l'ensemble des tâches que vous souhaitez le voir faire exécuter.

# Configuration de grunt

Créez puis ouvrez les fichier suivant avec votre éditeur favori.

```
% touch Gruntfile.js package.json
```

Nous allons tout d'abord installer le package node de grunt en utilisant le gestionnaire de packages de node.
Commencez par éditer le fichier package.json.

{% codeblock package.json %}
{
  "name": "grunt-article",
  "private": false,
  "description": "Grunt article source files",
  "version": "0.0.1",
  "author": {
    "name": "jtbonhomme"
  },
  "devDependencies": {
    "grunt" : "~0.4.5"
  }
}
{% endcodeblock %}

Sauvez et lancez la commande qui installe les dépendances node:

```
npm install
```

A cette étape, vous devez pouvoir exécuter grunt (l'option -v pour verbose):

```
% grunt -v
Initializing
Command-line options: --verbose

Reading "Gruntfile.js" Gruntfile...OK

Registering Gruntfile tasks.
Loading "Gruntfile.js" tasks...OK
>> No tasks were registered or unregistered.

No tasks specified, running default tasks.
Running tasks: default
Warning: Task "default" not found. Use --force to continue.

Aborted due to warnings.
```

On voit que grunt cherche dans le fichier Gruntfile.js les tâches à enregistrer et, en l'absence d'indication complémentaire, essaie de lancer une tâche <code>default</code>, qu'il ne trouve pas parce que, pour ceux qui suivent, nous n'avons encore rien écrit dans Gruntfile.js.

# La première tâche

Imaginons que nous compilons notre projet et que le résultat soit déposé dans un répertoire <code>public</code> et que nous souhaitons définir une tâche qui permette de vider le contenu de ce répertoire.

Nous allons utiliser un des nombreux plugins de grunt : <code>grunt-contrib-clean</code>

```
% npm install grunt-contrib-clean --save-dev
```

> l'option --save-dev permet d'ajouter automatiquement la dépendance au fichier package.json

Editez le fichier Gruntfile.js pour ajouter le code suivant:

{% codeblock Gruntfile.js %}
module.exports = function(grunt) {
  'use strict';

  // Project configuration.
  grunt.initConfig({

    clean: {
      all: [
        'public'
      ]
    }
  });


  // Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks('grunt-contrib-clean');

  // Default task(s).
  grunt.registerTask('default', ['clean:all']);

};
{% endcodeblock %}

Puis, créez un répertoire <code>public</code>, ajoutez-y un fichier <code>test</code> et lancez grunt:

```
% mkdir public
% touch public/test
% ls
Gruntfile.js node_modules package.json public
% grunt
Running "clean:all" (clean) task
Cleaning public...OK

Done, without errors.
% ls
Gruntfile.js node_modules package.json
```

Le répertoire <code>public</code> et son contenu ont bien été supprimés.

Nous avons créé notre première tâche grunt.

Nous verrons dans le prochain article comment utiliser les plugins et créer nos propres tâches.
