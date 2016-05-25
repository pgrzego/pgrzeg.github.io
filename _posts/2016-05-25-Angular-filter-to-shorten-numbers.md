---
title: Angular - filter to shorten numbers
tags: angular frontend
layout: post
---
I needed to fit bigger numbers into a small space on the website, so I've created a handy filter which checks how big the number is and if it can be shortened, it does that by dividing it and adding appropriate suffix (like 'k' or 'M'). The filter accepts a parameter to define the precision of the final number.
<!--more-->

### JavaScript

{% highlight js %}
angular
    .module('myApp',[])
    .controller('MainCtrl',['$scope', function($scope) {
        $scope.num = 1234567;
        $scope.pre = 0;
    }])
    .filter('NumberShortener', function() {
        return function(input, precision) {
            if ( input===undefined || input === null ) return input;
            var number = parseFloat(input);
            if (!isNaN(number)) {
              var postfix = "";
              if ( precision===undefined || isNaN(precision) ) precision = 0;
              if ( number > 1000000 ) {
                  number = number / 1000000;
                  postfix = "M";
              } else if ( number > 1000 ) {
                  number = number / 1000;
                  postfix = "k";
              }
              number = Math.round(number * Math.pow(10,precision)) / Math.pow(10,precision);
              return number+postfix;              
            } else {
              return 0;
            }
        }
    });
{% endhighlight %}

### HTML

{% highlight html %}{% raw %}
<!DOCTYPE html>
<html>

  <head>
    <script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/angular.js/1.4.8/angular.js"></script>
    <script src="script.js"></script>
  </head>

  <body ng-app="myApp">
    <div ng-controller="MainCtrl">
      Number: <input ng-model="num" type="text"><br>
      Precision: <input ng-model="pre" type="text"><br>
      {{num}} becomes {{num|NumberShortener:pre}}
    </div>
  </body>

</html>
{% endraw %}{% endhighlight %}

And [the plunker file to play with](https://plnkr.co/edit/u8YBSN).