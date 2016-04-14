---
title: Angular customized directive
tags: frontend, angularjs
---
It seems that knowing `$scope` is the first step to understand **AngularJS**. One of the next ones is how to create your own **directive** which will turn a custom HTML tag into a complex HTML DOM structure. I've been assigned with a task like that. And it wasn't as easy as I expected, especially once I had to take a step towards even more complex use case. Let's start where I've started - from ready directive which was waiting for me to change it (well I won't be talking about the exact same case, but similar enough).

----------
##The use case

There is a web page which is a showcase of mobile phone models. But the content of each module depends on the user who is logged in. Let's say the selection and comments depend on whether you are a premium customer or not. 

##The template

The module is designed as an Angular JS template. Which makes it easier later to reuse it in an HTML code. Here it is:

    <div class="col-lg-6 col-md-8">
        <div class="panel panel-{{color}}">
            <div class="panel-heading">
                <div class="row">
                    <div class="col-xs-3">
                        <img height="128" src="{{photo}}"/>
                    </div>
                    <div class="col-xs-9">
                        <h2>First place: <strong>{{model}}</strong></h2>
                        <div>
                            {{comment}}
                        </div>
                    </div>
                </div>
            </div>
    
        </div>
    </div>

As you can see it uses [Boilerplate](https://getbootstrap.com/) and there are four variables:

 - color of the module;
 - photo of the phone;
 - model of the phone;
 - comment on the phone.

This one will be saved as `customTpl.html` (you will see this file name later in the directive).

##HTML page

We can now use a custom HTML tag which will set all these parameters:

    <phone model="X500" photo="http://icongal.com/gallery/download/179985/256/png" color="warning" comment="The biggest DPI makes its screen crystal clear."></phone>

These are hard coded, but later on we will learn how to get them dynamically via AJAX calls.
What we need now is a directive to bind these two.

##The directive

I'll start with a code and I will explain what it does right after:
```javascript
    app.directive('phone', ['dynamic', function(dynamic) {
      return {
        restrict: 'E',
        replace: true,
        scope: {
          'comment': '@',
          'color': '@',
          'photo': '@'
        },
        templateUrl: 'customTpl.html',
        controller: function($scope) {
          fakeResponse = {
            "data": {
              "success": true,
              "data": "X300"
            }
          }
          $scope.model = fakeResponse.data.data;
        }
    
    
      }
    }]);

> Written with [StackEdit](https://stackedit.io/).