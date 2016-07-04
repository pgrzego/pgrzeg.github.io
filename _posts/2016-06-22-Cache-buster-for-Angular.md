---
layout: post
tags: angular frontend
title: Cache buster for Angular
comments: true
---
Every time there is an update of our Angular application, there is a need to force users' browsers to pull new versions of the files. And it's not as easy as it seems.
<!--more-->

My initial idea was to use the old trick with version number as a parameter of the file name. For example, in the `index.html` file I can use all the versions of the libraries that I use:

{% highlight html %}
<script src="bower_components/angular/angular.min.js?v=v1.5.6"></script>
<script src="bower_components/bootstrap/dist/js/bootstrap.min.js?v=1.3.2"></script>
<script src="bower_components/angular-ui-grid/ui-grid.min.js?v=3.1.1"></script>
{% endhighlight %}

And then each module loaded by Angular itself would use a constant app version:

{% highlight js %}
angular.module('app').constant('CONFIG', {
    version: '1.1b0'
});
{% endhighlight %}

Sample directive which uses the constant:

{% highlight js %}
angular.module('sbAdminApp')
    .directive('sidebar',['CONFIG',function(CONFIG) {
      return {
        templateUrl:'scripts/directives/sidebar/sidebar.html?v='+CONFIG.version,
        restrict: 'E',
        replace: true,
        controller:function($scope){
          // content of the controller
        }
      }
    }]);
{% endhighlight %}

This has proven not only to be difficult to maintain (before each release I needed to remember to make sure I increase the version value and each time I updated a bower component I also needed to reflect it in `index.html`), but ineffective. As it turns out it is not always working and on top of that when I change one internal Angular file, I need to increase a global version number which (if works) forces user to re-download all the files once again even though only one has been changed. And this is bad user experience.

Finally, I've decided to do it properly and implement what is called a cache buster. There are some in form of [plugins for Grunt](https://www.google.fr/search?client=firefox-b-ab&q=cachebuster+for+grunt) and I've decided to go with [grunt-cache-bust](https://github.com/hollandben/grunt-cache-bust).

What it does it calculates the hash (in a chosen algorithm) of the file and adds this hash into the file name. This way `angular.min.js` may become `angular.min.hash-c37eac4cb2170e60.js`. There are important advantages of this method:

1. The file name will change only when the content of the file changes, so no need to force users to download all files when only on is updated.
2. It can be done automatically with a Gruntfile, so no worries that I will forget to increase the version right before the update.

My configuration for the Yeoman built app:

{% highlight js %}
cacheBust: {
      dist: {
        options: {
          baseDir: '<%= yeoman.dist %>',
          assets: [                // All the JS, HTML and CSS files
          	'js/*.js', 
          	'scripts/**/*.js',
          	'scripts/**/*.html',
          	'styles/**/*.css',
          	'views/**/*.html', 
          	'bower_components/**/*.js', 
          	'bower_components/**/*.css'
          ],
          queryString: false,
          createCopies: true,
          deleteOriginals: false,  // keep minified originals for Git repository
          separator: '.hash-',     // in case any other script will need to easily recognize if the file is hashed or not
          jsonOutput: false
        },
        src: [                     // update links in index.html file, app.js (states) and directives templates
        	'<%= yeoman.dist %>/index.html', 
        	'<%= yeoman.dist %>/scripts/app.hash-*.js', 
        	'<%= yeoman.dist %>/scripts/directives/**/*.hash-*.js'
        ]
      }
    }
{% endhighlight %}

The other change I had to make was in `.gitignore` file:

{% highlight bash %}
*.hash-*.css
*.hash-*.js
*.hash-*.html
{% endhighlight %}

And that's it - works as charm since then and I don't need to worry about caching problems anymore (so far...).