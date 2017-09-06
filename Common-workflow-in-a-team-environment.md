# INTRODUCTION 

Le développement web devient de plus en plus exigent, plusieurs développeurs peuvent travailler en même temps sur un projet,chacun ayant une tache bien précise à exécuter dans le processus de développement. 

## Problème :

Chaque programmeur à sa manière de travailler, il a ses préférences en terme d'outils et de leurs configurations. (éditeur de texte, système d'exploitation, nomenclature des classes et espaces de noms, organisations du système de fichier, ...), Comment peuvent t'on alors travailler en équipe sur le même projet et en même temps tout en conservant chacun ses outils de préférences ? Ou tout simplement comment développer un produit de façon efficace sans avoir un problème de version, de dépendances, ...?

## Solution : _Github, Composer, Grunt._

Eh oui vous avez surement déjà entendu parler de ses outils, vous les utilisez probablement déjà, l'objectif ici n'est pas de faire de vous des "ninjas" de ces outils, mais de vous démontrer comment peut on coupler ces outils pour faciliter le processus de développement en équipe à défaut d'utiliser un framework comme **Symfony** ou **Laravel**. 

**COMPOSER**

Composer résout entre autre les problèmes suivants : 

* Résolution des dépendances pour les packages PHP
* Chargement automatique (autoloading) des packages php
* Maintenir tous les packages à jour

Si vous ne connaissez pas tout ça, lisez mon prochain tutoriel sur comment utiliser composer ou simplement la documentation officielle à https://getcomposer.org/doc/ 

Entrant dans le vif du sujet, lorsque vous travailler en équipe sur un projet web, procédez ainsi : 

**Etape 1 : Installer composer**

Assurez vous que tout le monde dans l'équipe l'a bien installer (consulter la doc officièl)


**Etape 2 : Exclure le dossier /Vendor générer automatiquement du control de revision**
  
Ce dossier contient les packages téléchargés par Composer. Il donc être exclu et chaque développeur doit maintenir sa propre copie de ce dossier dans son environnement locale. Il n'y a que le fichier _**composer.json**_ et _**composer.lock**_ qui doivent être commiter. (car cela ne servirait à rien de transférer et télécharger ce dossier à chaque modification car tout le monde on a dans son environnement). Pour ce faire, ajouter le tout simplement dans le fichier _**.gitignore**_ afin que git puisse l'ignorer. Il faut aussi exclure entre autre:

* DS_Store (génerer par OS X contenant les meta-donnée)
* Thumbs.db (similaire à DS_Store,contenant le cache pour les thumbnails)
* npm-debug.log (fichier de log de npm généré automatiquement )
* /bower_components (contient les packages téléchargés par bower : Gestionnaire de packages du front-end)
* /node_modules (contient les packages téléchargés par NPM, ce dossier peut être lourd à la fin)

**Etape 3 : Création du fichier _composer.json_**

le but de composer c'est de gérer les dépendances des packages. concrètement, dans un projet, on est souvent amené à utiliser les packages déjà existant, car on n'aime pas réinventer la roue en informatique.  S'il y à déjà un script qui fait très bien ce que tu veux faire dans ton application, autant l'intégrer si son créateur l'autorise. par exemple l'API php de Facebook, on ne va plus le recréer puisque Facebook l'a déjà fait pour nous et nous permet de l'utiliser gratuitement alors on se moment on l'intègre directement, c'est aussi souvent le cas avec les script comme le routage, la validation des données, les formulaires, plusieurs classe existent déjà qui gèrent très bien ses problématiques donc on va les utiliser dans notre application pour aller vite. 

Donc dans ce fichier, nous allons lister tous les packages que nous souhaitons utiliser dans notre application afin que composer les installe pour nous automatiquement. vous pouvez chercher et trouver les packages disponibles sur le site _https://packagist.org/_  et voir la configuration du fichier _composer.json_ sur la doc officielle.

Un petit exemple dans le cas de l'API Facebook php : (composer.json)
`{`
  `"require": {`
    	`"facebook/php-sdk": "3.2.*"`
  `}`
`}`

**Etape 4 : Installer les packages requis**

Executer simplement(chacun dans son environnement) la commande suivante en ligne de commande : 
`composer install --no-dev`. Composer va tout seul comme un grand télécharger et installer dans votre environnement local (dossier **Vendor**) tous les packages listés dans le fichier _composer.json _ 

**Etape 5: Les membres de l'équipe peuvent à leur tour exécuter la commande: `composer install --no-dev`**

Dès lors que le fichier _composer.json_ est committé via git (voir ce que cela signifie sur 
https://developer.github.com/v3/repos/commits/) tous les développeurs ayant accès au dépôt du projet (donc travaillant sur le projet) peuvent installer les packages sur leur machine respective en exécutant la commande.

**Etape 6 : Forcer l'optimisation du chargeur automatique (**autoload**)**

Il est possible d'obtenir un **autoload** optimisé en ajoutant le paramètre ci-dessous dans le fichier _composer.json_

`{`
  `"require": {`
    	`"facebook/php-sdk": "3.2.*",`
    `},`
    `"config": {`
    	`"optimize-autoloader": true`
    `}`
`}`
 
Ça rend la génération de _l'autoload_ plus lent mais c'est très recommandé dans la production.

**Etape 7 : Suivre les packages et obtenir les notifications sur leur éventuel mise à jour**
 
Je vous recommande d'utiliser _versioneye.com_, vous suivez les packages utilisés par votre application et à chaque fois  qu'il y a des mises à jour disponibles il vous envoie un mail de notification; cela vous permet de rester à jour.
 
**Etape 8 : Vérifier pour connaitre les éventuels ou possibles failles de sécurité**
 
La sécurité c'est très important, personnellement j'y accorde une place de choix. Je vous recommande donc d'utiliser https://security.sensiolabs.org/, vous pourriez uploader votre fichier _composer.lock_ et vous serez toute suite avertie en cas de possible failles de sécurité pour les packages que vous utilisez dans votre application.

**Etape 9 : Interagir  avec les outils d’automatisation**

**Grunt** et **Gulp **par exemple sont des _build tools_, utilisé pour automatiser les taches communes et récurrentes. comme minifier les scripts, optimiser les images, minifier les css, compiler les Less/Sass/Stylus. 

Jusque la, dès que l'un des développeur modifie le fichier _composer.json_, tout le monde est obligé de faire une mise à jour via la commande composer `install --no-dev`. Pareil si le fichier _composer.lock_(qui contient les versions de chaque packages utilisés) est modifié. Ce comportement est très embêtant vue qu'on sera souvent amener à modifier ces fichiers donc il serait très intéressant si l'on pouvait faire de façon à ce que cela se fasse automatiquement. et c'est le but justement de **Grunt** ou **Gulp**

Voyant comment on peut faire cela(exemple avec Grunt) : 

`watch: {`
        `composer_json: {`
                `files: [ 'composer.json', 'composer.lock' ],`
                `tasks: [ 'exec:composer_install' ],`
        `}`
`}`

`exec: {`
        `composer_install: {`
                `cmd: 'composer self-update && composer install --no-dev',`
                `exitCode: [ 0, 255 ]`
        `}`
`}`

Ainsi,nous sommes sûr que composer sera toujours à jour (composer self-update) et que tous nos packages sont synchroniser avec les autres développeurs (_composer.lock_). Le code [0,255] permet à la commande d'échouer sans pour autant tuer le processus _Grunt_. Génial non :) 

> Je vais d'abord m’arrêter la, j'ai plus assez d'énergie :D je reviendrais ici pour mettre le wiki à jour. 
Je reste ouvert à toutes suggestions, remarques.  
