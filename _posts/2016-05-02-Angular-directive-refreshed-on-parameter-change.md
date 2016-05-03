---
title: Angular directive refreshed on parameter change
tags: angular frontend
layout: post
---
I'm using a [SB Admin Angular template](http://startangular.com/product/sb-admin-angular-theme/) to create a dashboard. The main page displays few simple indicators. The data for them is retrieved with an AJAX query calling a RESTful web service. It's all working very well. What I needed to do now was to enhance it - user was supposed to be able to change the date range and each time some of the indicators were supposed to change their values to reflect the time period.

Here is [the working plunker](https://plnkr.co/edit/a9Kj9Kar2maZ2m9dfaNL) to show you an example of how to tackle this requirement. And below I will briefly explain what is happening.

#### index.html

{% highlight html %}{% raw %}
<!DOCTYPE html>
<html ng-app="myApp">

  <head>
    <link rel="stylesheet" href="style.css">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.16/angular.js"></script>
    <script src="script.js"></script>
  </head>

  <body ng-controller="MainCtrl">
    <h1>Hello {{user}}!</h1>
    <select name="singleSelect" ng-model="customNumber">
                <option value="0">0</option>
                <option value="1">1</option>
                <option value="2">2</option>
            </select>
    <stats number="{{customNumber}}"></stats>
  </body>

</html>
{% endraw %}{% endhighlight %}


#### tpl.html

{% highlight html %}{% raw %}
<p>New number is {{number}} and it is a {{opinion}} number.</p>
{% endraw %}{% endhighlight %}

#### script.js

{% highlight js linenos %}{% raw %}
angular
    .module('myApp', [])
    .controller('MainCtrl', ['$scope', '$http', function($scope, $http, $position) {
      $scope.user = "You";
      $scope.customNumber = 0;

    }])
    .factory('onlineData', ['$http', function($http) {
      var service = {};
      service.update = update;
      return service;
      
      function update(number, callback) {
        $http({
            url: "json.js",
            method: "GET",
            cache: false,
            params: {
                no: number
            }
        }).then( function(response) {
            callback(response);
        }, function errorCallback(response) {
            
        } )
      }
      
    }])
    .directive('stats',['onlineData', function(onlineData) {
        return {
            templateUrl:'tpl.html',
            restrict:'E',
            replace:true,
            scope: {
              'number': '@'
            },
          link: function(scope, element, attrs) {
              // Stuff that happens after the template has been rendered
              // jQuery and other DOM manipulations
              scope.$watch('number', function(newValue, oldValue) {
                  if (newValue === oldValue) { return; }
                  // Update AJAX:
                  onlineData.update(scope.number,function(response){
                    console.log(JSON.stringify(response));
                    scope.opinion = response.data[scope.number]
                  });
                  console.info("Task has changed and is now "+newValue);
              }, true);
  
          },
          controller: function($scope, onlineData) {
              onlineData.update($scope.number,function(response){
                $scope.opinion = response.data[$scope.number]
              });
          }
            
        }
    }]);
{% endraw %}{% endhighlight %}

#### json.js

{% highlight js %}{% raw %}
{
  "1": "small",
  "2": "even",
  "0": "round"
}
{% endraw %}{% endhighlight %}

### Webography:
1. Inspired by [an answer I found on stackoverflow](https://stackoverflow.com/questions/20856824/angular-directive-refresh-on-parameter-change).
2. Very good explanation on [Difference between controller and link](http://jasonmore.net/angular-js-directives-difference-controller-link/) in directives.
3. My starting point to understand binding process in Angular: [$watch How the $apply Runs a $digest](http://angular-tips.com/blog/2013/08/watch-how-the-apply-runs-a-digest/).