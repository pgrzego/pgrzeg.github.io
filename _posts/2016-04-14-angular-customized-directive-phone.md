---
title: Angular customized directive
tags: frontend, angularjs
---
It seems that knowing `$scope` is the first step to understand **AngularJS**. One of the next ones is how to create your own **directive** which will turn a custom HTML tag into a complex HTML DOM structure. I've been assigned with a task like that. And it wasn't as easy as I expected, especially once I had to take a step towards even more complex use case. Let's start where I've started - from a ready directive which was waiting for me to change it (well I won't be talking about the exact same case, but similar enough).

### Beforehand

If you're not sure if you know enough about Angular JS, check out [Matthew Carriere's post "Controllers, Directives, and Services. AngularJS 101"](http://matthewcarriere.com/2015/01/13/controllers-directives-services-angularjs-101/).

----------

## The use case

There is a web page which is a showcase of mobile phone models. But the content of each module depends on the user who is logged in. Let's say the selection and comments depend on whether you are a premium customer or not. 

## The template

The module is designed as an Angular JS template. Which makes it easier later to reuse it in an HTML code. Here it is:

```html
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
                        {{comment2}}
                    </div>
                </div>
            </div>
        </div>

    </div>
</div>
```

As you can see it uses [Boilerplate](https://getbootstrap.com/) and there are four variables:

 - color of the module;
 - photo of the phone;
 - model of the phone;
 - comment on the phone.

This one will be saved as `customTpl.html` (you will see this file name later in the directive).

## HTML page

We can now use a custom HTML tag which will set all these parameters:

```html
<phone model="X500" photo="http://icongal.com/gallery/download/179985/256/png" color="warning" comment="The biggest DPI makes its screen crystal clear."></phone>
```

These are hard coded, but later on we will learn how to get them dynamically via AJAX calls.
What we need now is a directive to bind these two.

## The directive

I'll start with a code and I will explain what it does right after:

{% highlight javascript linenos %}
app.directive('phone', function() {
  return {
    restrict: 'E',
    replace: true,
    scope: {
      'comment': '@',
      'brand': '@',
      'color': '@',
      'photo': '@'
    },
    templateUrl: 'customTpl.html',
    controller: function($scope) {
      fakeResponse = {
        "data": {
          "success": true,
          "model": "X300"
        }
      }
      $scope.model = fakeResponse.data.model;
    }
  }
});
{% endhighlight %}

### Restrict
`restrict` 






## Further reading

While working on this one I've stumbled across some very good questions and answers which were not directly related, but helped me expand my knowledge more:

1. Should you need to notify Angular that the `scope` was updated which is somehow explained [here](https://stackoverflow.com/questions/16066170/angularjs-directives-change-scope-not-reflected-in-ui). 
2. If you want to support default values and additional tags [this one](https://stackoverflow.com/questions/10629238/angularjs-customizing-the-template-within-a-directive) should be useful.
3. [This plunker](http://jsbin.com/acibiv/4/edit) is a good example for recursive directives. I found it in the comments for [this post](https://sporto.github.io/blog/2013/06/24/nested-recursive-directives-in-angular/).

> Written with [StackEdit](https://stackedit.io/).