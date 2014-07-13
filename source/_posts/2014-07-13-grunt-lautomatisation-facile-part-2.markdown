---
layout: post
title: "Grunt: L’automatisation Facile (part 2)"
date: 2014-07-13 10:23:27 +0200
comments: true
categories: grunt, javascript
---

![](http://gruntjs.com/img/grunt-logo.svg)

Nous avons vu dans l'article précédent [Grunt: l'automatisation facile (part 1)]({% post_url 2014-07-05-grunt-lautomatisation-facile-part-1 %}) comment installer et configurer Grunt.
Nous allons dans aujourd'hui utiliser des plugins pour aller plus loin et créer nos propres tâches.

# Les plugins

Grunt est bien supporté par la communauté, et la multitude de [plugins disponibles](http://gruntjs.com/plugins) pourront vous rendre de grands services.

Quelques exemples parmi les plus connus:

* [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch) : permet de surveiller une arborescence de fichier de de déclencher des actions en cas de modification de l'un d'eux (voir exemples ci-dessous)
* [grunt-contrib-copy](https://github.com/gruntjs/grunt-contrib-copy) : comme le nom l'indique, permet de copier des fichiers d'un répertoire A à un répertoire B
* [grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat) : concatène plusieurs fichiers en un seul
* [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify) : "minifie" des fichiers en utilisant la librairie UglifyJS
* [grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint) : linter javascript utilisant la librairie [JSHint](http://www.jshint.com/) (voir exemple plus bas)
* [grunt-shell](https://github.com/sindresorhus/grunt-shell) : permet de définir des tâches custom utilisant le shell (voir exemple plus bas)

# Pour aller plus loin ...

Pour ceux qui souhaitent approfondir un peu, vous trouverez ci-dessous quelques exemples complémentaires et un paragraphe sur l'utilisation de tâches personnalisées.

## Quelques exemples d'utilisation des plugins

#### grunt-contrib-jshint

JSHint permet de détecter dans le code javascript des erreurs de syntaxe simples (oubli d'un ';' à la fin d'une ligne, oubli d'une accolade à la fin d'un bloc, utilisation de noms de variable non conforme, ...)

Il s'installe de la manière suivante:

```
% npm install grunt-contrib-jshint --save-dev
```
Et il se configure dans le Gruntfile suivant l'exemple ci-dessous, qui va vérifier le fichier <code>Gruntfile.js</code> lui-même, et tous les fichiers javascript se trouvant dans le répertoire <code>js</code> :

{% codeblock Gruntfile.js %}
module.exports = function(grunt) {
  'use strict';

  // Project configuration.
  grunt.initConfig({

   jshint: {
      files: [
        'Gruntfile.js',
        'js/**/*.js'
      ],
      options: {
      }
    }

  });


  // Load the plugin that provides the "jshint" task.
  grunt.loadNpmTasks('grunt-contrib-jshint');

  // Default task(s).
  grunt.registerTask('default', ['jshint']);

};
{% endcodeblock %}

Puis il vous suffit d'exécuter la commande <code>grunt</code>:

```
% grunt
Running "jshint:files" (jshint) task
>> 1 file lint free.

Done, without errors.
```

On voit que le fichier <code>Gruntfile.js</code> a été vérifié avec succès.

#### grunt-contrib-watch

Le plugin watch permet de déclencher des tâche lorsque des fichiers sont modifiés.

Il s'installe de la manière suivante:

```
% npm install grunt-contrib-watch --save-dev
```
Et il se configure dans le Gruntfile suivant l'exemple ci-dessous :

{% codeblock Gruntfile.js %}
module.exports = function(grunt) {
  'use strict';

  // Project configuration.
  grunt.initConfig({

    dirs: {
      app:     'app/',
      js:      'app/js/',
      css:     'app/css/',
      public:  'public/'
    },

    files: {
      all: '**/*',
      js:  '**/*.js',
      css: '**/*.css',
      img: '**/*.{png,gif,jpg,jpeg}'
    },

    watch: {
      jshint: {
        files: ['<%= dirs.js %><%= files.js %>', '<%= dirs.public %><%= files.js %>', 'Gruntfile.js'],
        tasks: 'jshint'
      }
    }

  });


  // Load the plugin that provides the "watch" task.
  grunt.loadNpmTasks('grunt-contrib-watch');

  // Default task(s).
  grunt.registerTask('default', ['watch:jshint']);

};
{% endcodeblock %}

On en profite pour ajouter 2 hash qui permettront ensuite de gagner du temps: <code>dirs</code> et <code>files</code> et qui définissent les répertoires et catégories de fichiers qui seront utilisés ensuite dans les tâches.
Vous retrouvez les 2 lignes qui chargent le plugin (<code>grunt.loadNpmTasks('grunt-contrib-watch');</code>) et enregistrent votre tâche (<code>grunt.registerTask('default', ['watch:jshint']);</code>)

La configuration du pluging comporte une section qui défini la tâche à lancer <code>jshint</code> et quels sont les fichiers à surveiller.
Dans notre cas, nous allons linter tous les fichiers js qui se trouvent dans les répertoires <code>js</code> et <code>public</code>, ainsi que le fichier <code>Gruntfile.js</code>.

Il ne reste plus qu'à exécuter la tâche :

```
% grunt watch:jshint
Running "watch:jshint" (watch) task
Waiting...
```

Encore un petit truc, si vous n'avez pas enregistré de tâche personnelles avec la propriété <code>registerTask</code>, vous pouvez toujours exécuter les tâches définies dans le <code>initConfig()</code>.

Il ne nous reste plus qu'à provoquer un changement dans le fichier <code>Gruntfile.js</code> en supprimant un point-virgule en fin de ligne par exemple (ne pas oublier de sauver le fichier)

```
% grunt watch:jshint
Running "watch:jshint" (watch) task
Waiting...

Reloading watch config...

Running "watch:jshint" (watch) task
Waiting...
>> File "Gruntfile.js" changed.
Running "jshint:files" (jshint) task

   Gruntfile.js
     47 |  })
             ^ Missing semicolon.

>> 1 error in 1 file
Warning: Task "jshint:files" failed. Use --force to continue.

Aborted due to warnings.
Completed in 0.946s at Sun Jul 06 2014 10:01:10 GMT+0200 (CEST) - Waiting...
```

Si on corrige l'erreur:

```
% grunt watch:jshint
Running "watch:jshint" (watch) task
Waiting...

Reloading watch config...

Running "watch:jshint" (watch) task
Waiting...
>> File "Gruntfile.js" changed.
Running "jshint:files" (jshint) task

   Gruntfile.js
     47 |  })
             ^ Missing semicolon.

>> 1 error in 1 file
Warning: Task "jshint:files" failed. Use --force to continue.

Aborted due to warnings.
Completed in 0.946s at Sun Jul 06 2014 10:01:10 GMT+0200 (CEST) - Waiting...

Reloading watch config...

Running "watch:jshint" (watch) task
Waiting...
>> File "Gruntfile.js" changed.
Running "jshint:files" (jshint) task
>> 1 file lint free.

Done, without errors.
Completed in 1.095s at Sun Jul 06 2014 10:02:58 GMT+0200 (CEST) - Waiting...
```

Ouf, la catastrophe est évitée.

#### grunt-shell

Le dernier plugin que nous allons étudier est le plugin <code>grunt-shell</code>.
Ce plugin permet d'exécuter des commandes système dans grunt.

Dans notre exemple, nous allons créer une tâche qui réalise une commande <code>git log</code>.
On pourra plus tard, réaliser un "rsync" pour déployer son application sur un serveur de préproduction.

Vous connaissez la manipulation, on commence par installer le plugin:

```
% npm install grunt-shell --save-dev
```
Et on le configure dans le Gruntfile suivant l'exemple ci-dessous :

{% codeblock Gruntfile.js %}
module.exports = function(grunt) {
  'use strict';


  // Shell logging function
  function logShell(err, stdout, stderr, cb) {
    if (err) {
      grunt.log.error('Command failed on ' + new Date());
    } else {
      grunt.log.ok('Command executed on ' + new Date());
    }
    cb();
  }

  // Project configuration.
  grunt.initConfig({

    dirs: {
      app:     'app/',
      js:      'app/js/',
      css:     'app/css/',
      public:  'public/'
    },

    files: {
      all: '**/*',
      js:  '**/*.js',
      css: '**/*.css',
      img: '**/*.{png,gif,jpg,jpeg}'
    },

    shell: {
      options: {
        failOnError: true,
        callback: logShell,
        stdout: true,
        stderr: true
      },
      gitlog: {
        command: 'git log'
      }
    }
  });


  // Load the plugin that provides the "shell" task.
  grunt.loadNpmTasks('grunt-shell');

  // Default task(s).
  grunt.registerTask('default', ['shell:gitlog']);

};
{% endcodeblock %}

La section de configuration de la tâche <ode>shell</code> contient 2 sous-sections.
La première configure de manière générale le plugin. On remarque qu'il est possible d'appeler une callback (nous avons défini une fonction <code>logShell</code>) et d'activer les sorties standards stdout et stderr.
Les sous-sections suivantes définissent les appels systèmes.

Essayons :

```
% grunt shell:gitlog
Running "shell:gitlog" (shell) task
commit 8a797952e14d0e5301432fce2dca8d944e172f36
Author: Jean-Thierry BONHOMME <jtbonhomme@gmail.com>
Date:   Sat Jul 5 17:04:32 2014 +0200

    Added jshint task

commit 85fc208f8e2ee257a782d0787c4233d3167044a8
Author: Jean-Thierry BONHOMME <jtbonhomme@gmail.com>
Date:   Sat Jul 5 16:18:21 2014 +0200

    Update article reference)

commit 752d0c8b6cc74c6a98417d43e42ba486020aa4db
Author: Jean-Thierry BONHOMME <jtbonhomme@gmail.com>
Date:   Sat Jul 5 16:17:34 2014 +0200

    First commit

commit 4b6649ed0c838723565649c6fbc0189bec19c874
Author: Jean-Thierry BONHOMME <jtbonhomme@gmail.com>
Date:   Sat Jul 5 16:14:15 2014 +0200

    Initial commit
>> Command executed on Sun Jul 06 2014 10:22:28 GMT+0200 (CEST)

Done, without errors.
```
On est bien d'accord que c'est juste un exemple, et que cette tâche ne présente aucun intérêt pratique, il serait évidemment plus simple d'entrer la commande <code>% git log</code>...

## Créer ses propres tâches  

La [documentation grunt](http://gruntjs.com/creating-tasks) détaille la manière de créer ses propres tâches.

#### Tâches personnalisées simples

Le plus simple pour créer une tâche est de la développer directement dans le Gruntfile :

```
grunt.registerTask('foo', 'My "foo" task.', function() {
  grunt.log.writeln('Execution de la tache "foo"');
});
```

Puis appelez la directement :

```
% grunt foo
Running "foo" task
Execution de la tache "foo"

Done, without errors.
```

C'est relativement pratique pour les scripts simple. Pour développer des tâches plus complexes, voici comment procéder.

#### Tâches personnalisées complexes

Les tâches personnalisées sont stockées dans un répertoire qui peut être chargé par grunt. Dans notre cas, nosu avons créé une tâche <code>hash</code> (merci [@jinroh](https://github.com/jinroh)) qui calcul un hash md5 sur des fichiers.

On modifie les Gruntfile comme suit:

{% codeblock Gruntfile.js %}
module.exports = function(grunt) {
  'use strict';

  grunt.loadTasks('tasks');

  // Project configuration.
  grunt.initConfig({

    // custom variables configuration
    dirs: {
      app:     'app/',
      js:      'app/js/',
      css:     'app/css/',
      public:  'public/'
    },

    files: {
      all: '**/*',
      js:  '**/*.js',
      css: '**/*.css',
      img: '**/*.{png,gif,jpg,jpeg}'
    },

    hash: {
      options: {
        prefix: './'
      },
      publics: {
        src: [
          'Gruntfile.js'
          ],
        dest: '<%= dirs.public %>hash'
      }
    }

  });

  // Default task(s).
  grunt.registerTask('default', ['watch']);

};
{% endcodeblock %}

Puis on crée un répertoire <code>tasks</code> contenant un fichiers <code>hash.js</code>:

{% codeblock tasks/hash.js %}
/* jshint node:true */

var crypto = require('crypto');

module.exports = function(grunt) {
  'use strict';

  function md5(file) {
    var data = grunt.file.read(file, { encoding: null });
    return crypto.createHash('md5').update(data).digest('hex');
  }

  grunt.registerMultiTask('hash', 'Create the hash file', function() {
    var prefix = new RegExp('^' + this.options().prefix);

    var hashes = [];
    var maxlen = 0;

    this.files.forEach(function(files) {
      files.src.map(function(file) {
        var name = file.replace(prefix, '') + ':';
        maxlen = Math.max(maxlen, name.length);
        return [name, md5(file)];
      }).forEach(function(data) {
        hashes.push(grunt.log.table([maxlen + 4, 32], data));
      });
    });

    hashes = hashes.join('\n') + '\n';

    grunt.log.writeln(hashes);
    grunt.log.write('Generating ' + this.data.dest.cyan + '...');
    grunt.file.write(this.data.dest, hashes);
    grunt.log.ok();
  });

};
{% endcodeblock %}

Il ne reste plus qu'à lancer la tâche :

```
% grunt hash
Running "hash:publics" (hash) task
Gruntfile.js:    7d0428cb4f97db4bed4ce04e21eac969

Generating public/hash...OK

Done, without errors.
```

## Conditions d'échec

Il faut noter que lorsqu'on défini une tâche comme une suite d'autres tâches, toute erreur sur une de ces tâches provoque l'arrêt de la tâche 'parente'.

Par exemple l'exécution de la suite de tâche ci-dessous va échouer lors de la 2ème tâche (ko2) sans exécuter la 3ème (ok3) :

```
grunt.registerTask('ok1', 'This task succeds', function() {
  grunt.log.writeln('Execution de la tache "ok1"');
  return true;
});

grunt.registerTask('ko2', 'This task fails', function() {
  grunt.log.writeln('Execution de la tache "ko2"');
  return false;
});

grunt.registerTask('ok3', 'This task succeds', function() {
  grunt.log.writeln('Execution de la tache "ok3"');
  return true;
});

grunt.registerTask('ok-ko', ['ok1', 'ko2', 'ok3']);
```

```
% grunt ok-ko
Running "ok1" task
Execution de la tache "ok1"

Running "ko2" task
Execution de la tache "ko2"
Warning: Task "ko2" failed. Use --force to continue.

Aborted due to warnings.
```

## Liens pour aller (encore) plus loin

* [Advanced Grunt Tooling](http://chrisawren.com/posts/Advanced-Grunt-tooling)
* [Perfecting your Grunt Workflow](http://blog.gleitzman.com/post/73110295075/perfecting-your-grunt-workflow)
* [Grunt with express server and Livereload](http://thanpol.as/grunt/Grunt-with-express-server-and-Livereload/) : pour servir votre site en local et rafraîchir votre navigateur dès que le code change

# Conclusion

Je vous conseille de jeter un oeil aux plugins qui permettent d'intégrer les framework de test dans grunt ([grunt-mocha](https://github.com/kmiyashiro/grunt-mocha) par exemple) et qui permettent d'aller très loin dans l'automatisation des tâches (corvées ?) courantes.
Vous pouvez aussi examiner le module [grunt-init](http://gruntjs.com/project-scaffolding) qui permet d'initialiser des projets rapidement suivant des TEMPLATES prédéterminés.

La communauté est très active autour de ce projet, vous trouverez surement le plugin qu'il vous faut !

Enfin, pour supporter Grunt, vous pouvez d'ailleurs ajouter aux <code>README.md</code> de vos propres projets, la ligne

```
[![Built with Grunt](https://cdn.gruntjs.com/builtwith.png)](http://gruntjs.com/)
```

Ce qui fera apparaître le badge suivant :

[![Built with Grunt](https://cdn.gruntjs.com/builtwith.png)](http://gruntjs.com/)
