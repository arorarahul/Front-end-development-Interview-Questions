# Front-end-development-Interview-Questions-with-Answers

Technologies covered

- Javascript
- Angularjs
- HTML
- CSS

References:

- My personal experience
- [Reference1](https://github.com/h5bp/Front-end-Developer-Interview-Questions)
- [Reference2](https://www.codementor.io/angularjs/tutorial/angularjs-interview-questions-sample-answers)

## Javascript
 
- [What all design patterns do you know? Which one should be used at what instance?](#design-patterns)
- [What are promises?](#promises)
- [How will you handle a task that is to be done after successful completion of multiple asynchronous calls?](#multiple-async-call-handling-using-promises)
- [List all the advantages and disadvantages you know of using strict mode in javascript.](#use-strict)
- [What is the application of closures? Why use them over normal functions?](#javascript-closures)
- [Make a simple counter using a single function and a local variable using closures](#simple-counter-function)
- [Devise a solution in which an object can be made using a constructor function without adding `new` when calling the constructor function and assigning to the object?](#making-objects-using-constructor-function-without-newing)

## AngularJS

- [What is the difference between $scope.$apply and $scope.$$digest](#apply-and-digest-in-angular)
- [What are the different methods to do directive to directive communication in AngularJS](#directive-to-directive-communication)
- [What is the difference between transclude and replace in directives in AngularJS](#transclude-and-replace)
- [What is the difference between compile and link?](#compile-and-link)
- [What are the various scope options available while making directives. Explain each of them in detail?](#scope-options-in-directives)
- [What are the ways to optimize an AngularJS application](#optimising-your-angular-app)
- [What is the difference between adding $scope and scope in link function in a directive]()
- [What is the difference between service, provider and factory](#service-provider-and-factory)
- [What is the difference between config and run in AngularJS]($config-and-run)
- [When to use a directive over a component](#components-vs-directives)
- [What is the key difference between Angular 1.x and Angular2](#angular1.x-vs-angular2)
- [Using $cacheFactory]()





## Design Patterns

- Module
- Prototype
- Observer
- Singleton


### Module Design Pattern

- [Reference1](https://scotch.io/bar-talk/4-javascript-design-patterns-you-should-know)
- [Reference2](https://toddmotto.com/mastering-the-module-pattern/)

*Advantages*:

Used for keeping particular pieces of code independent of other components.
Modules are nothing but classes that provide encapsulation protecting behaviour and states from being accessed from other classes. Modules should be an IIFE and should return an object
It makes scoping easy and does not overcomplicate your code

*Disadvantages*:

This can pose some unit testing challenges as private methods are not accessible

```JS
(function(){
	
	//delcare private variables and functions here

	return {
		//declare public variables and functions
	}

})();
```

```JS
//first module
var HTMLChanger = (function(){

	var content = 'contents';

	var changeHTML = function(){
		var element = document.getElementById('someElement');
		element.innerHTML = content;
	}

	return {
		callChangeHTML: function(){
			changeHTML();
			console.log(content);
		}
	}

})();

//using the module
HTMLChanger.callChangeHTML();
console.log(content); //undeclared variable contents

//Revealing module pattern 
var Exposer = (function(){

	var privateVariable = 10;

	var privateMethod = function(){
		privateVariable++;
	}

	var methodToExpose = function(){
		console.log('this is a method that I want to expose');
	}

	var otherMethodIWantToExpose = function(){
		privateMethod();
	}
	//revealing the elements here
	return{
		first: methodToExpose,
		second: otherMethodIWantToExpose
	}

})();

Exposer.methodToExpose; //gives undefined
Exposer.first();
```

Example of passing data to a private method from outside the module

```JS

var Module = (function(){
	
	var privateMethod = function(text){
		console.log(text);
	}

	var publicMethod = function(text){
		privateMethod(text);
	}

	return{
		publicMethod: publicMethod
	}

})();

Module.publickMethod('Hello');
```

### Augmenting Modules

What if we wanted to create extensions of our module and wanted to maintain the privacy

```JS

var Module = (function(){
	
	var privateMethod = function(text){
		console.log(text);
	}

	var publicMethod = function(text){
		privateMethod(text);
	}

	return{
		publicMethod: publicMethod
	}

})();

var ModuleTwo = (function(module){

	var extension = function(){
		console.log("Added an extension");
	}

	module.extension = extension;

	return module;

})(Module || {});

console.log(Module.extension());
console.log(Module.publicMethod('hello'));
```

Adding an `_` infront of a method name is a good practice to distinguish a private method from a public method


### Prototype Design Pattern

- [Reference1](https://scotch.io/bar-talk/4-javascript-design-patterns-you-should-know)
- [Reference2](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)

*Advantages*:

Whenever some extensive operation on database is to be performed, create an object that can be used for other parts of the application. If another process needs to use this object, instead of performing the database operation, previous object can be cloned.

For example this pattern can be used in the initialization of business objects with values that match the default values in the database. The prototype object will then hold the values that will be copied over to newly created objects using that. 

*Disadvantages*:

```JS
var Person = function(){

	this.firstname = "rahul";
	this.lastname = "arora";
	
}

Person.prototype = (function(){

	var getFullName = function(){
		return this.firstname  + this.lastname;
	}

	var lastName = function(){
		return this.lastname;
	}

	return{
		first: getFullName,
		second: lastName
	}

}());
```

```JS
//global object to be used
var vehicle = {
	init: function(model){
		this.model = model;
	},
	getModel: function(){
		return this.model;
	}
};
//module returning a prototype
var hello = (function(){

	function F(){};

	return function(proto){
		F.prototype = proto;
		return new F();
	}

})();

//using global object and module together to make a new object
var car = hello(vehicle);
car.init('maruti');
```


### Observer Design Pattern

- [Reference1](https://scotch.io/bar-talk/4-javascript-design-patterns-you-should-know)
- [Reference2](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)

If an object is modified, it broadcasts to the dependent objects that a change has occured.

*Disadvantage*

Significant drop in performance as the number of observers increase

In AngularJS, we can watch variables, functions, and objects. The $$digest cycle runs and notifies each of the watchers with the new values whenever a scope object is modified.

Doing the same in javascript:

```JS
var Subject = function() {
  this.observers = [];

  	return {
	    subscribeObserver: function(observer) {
	      this.observers.push(observer);
	    },
	    unsubscribeObserver: function(observer) {
	      var index = this.observers.indexOf(observer);
	      if(index > -1) {
	        this.observers.splice(index, 1);
	      }
	    },
	    notifyObserver: function(observer) {
	      var index = this.observers.indexOf(observer);
	      if(index > -1) {
	        this.observers[index].notify(index);
	      }
	    },
	    notifyAllObservers: function() {
	      for(var i = 0; i < this.observers.length; i++){
	        this.observers[i].notify(i);
	      };
	    }
  	};
};

var Observer = function() {
  	return {
    	notify: function(index) {
      		console.log("Observer " + index + " is notified!");
    	}
  	}
}

var subject = new Subject();

var observer1 = new Observer();
var observer2 = new Observer();
var observer3 = new Observer();
var observer4 = new Observer();

subject.subscribeObserver(observer1);
subject.subscribeObserver(observer2);
subject.subscribeObserver(observer3);
subject.subscribeObserver(observer4);

subject.notifyObserver(observer2); // Observer 2 is notified!

subject.notifyAllObservers(); 
```

### Singleton Pattern

- [Reference1](https://scotch.io/bar-talk/4-javascript-design-patterns-you-should-know)
- [Reference2](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)
- [Reference3](http://robdodson.me/javascript-design-patterns-singleton/)

Singleton only allows for single instantiation of the same object.


## Promises

- [Reference1](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Reference2](http://www.mattgreer.org/articles/promises-in-wicked-detail/#why-)
- [Reference3](https://github.com/kriskowal/q)
- [Reference4](http://chariotsolutions.com/blog/post/angularjs-corner-using-promises-q-handle-asynchronous-calls/)

A promise is a value which may be available now, or in future or never when handling async operations

### Note:

The defer() function was discontinued as the new Promise constructor thing was throw safe. In the Promise constructor if there is an error coming, you can simply handle it unlike in the defer() method where the error is thrown synchronously and you will have to handle the error manually

- [Reference](http://stackoverflow.com/questions/28687566/difference-between-defer-promise-and-promise)


```JS
var a = new Promise(function(resolve, reject){

  var xhttp = new XMLHttpRequest();

  xhttp.onreadystatechange = function(){
    if(this.readyState == 4 && this.status == 200){
      resolve(xhttp.responseText);
    }
  }

  xhttp.open("GET","data.json", true);
  xhttp.send();

});

a.then(function(res){
  console.log(res);
}).catch(function(err){
  console.log(err);
});
```

### Promises in AngularJS

Writing your own $http, simple example using promises

```JS
var $http = (function() {

    return {
        get: function(url) {
            return new Promise(function(resolve, reject) {
                var xhttp = new XMLHttpRequest();
                xhttp.onreadystatechange = function() {
                    if (this.readyState == 4 && this.status == 200) {
                        resolve(xhttp.responseText);
                    }
                }
                xhttp.open('GET', url, true);
                xhttp.send();
            });
        }
    }

})();

$http.get('data.json').then(function(response) {
    console.log(response);
});
```

### Using the $q service of AngularJS

**This article has been intentionally included here to make a comparison with Promise contructor in javascript and $q service of AngularJS**

```JS
angular.module("myApp",[])
.controller("myController",function($scope, myService){

    var p1 = myService.getData();

    p1.then(function(response){
        console.log(response);
    }, function(error){
        console.log(error);
    })

})
.service("myService",function($http, $q){

    this.getData = function(){

        return $q(function(resolve, reject){

            $http.get('data.json').then(function(response){
                resolve(response);
            }, function(error){
                reject(response);
            })  

        });

    };

});
```

The above code can also be written using deferred:

A new promise instance is created when a deferred instance is created and can be retrieved by calling deferred.promise

```JS
angular.module("myApp",[])
.controller("myController",function($scope, myService){

    var p1 = myService.getData();
    // console.log(p1.$$state);
    p1.then(function(response){
        console.log(response);
    }, function(error){
        console.log(error);
    })

})
.service("myService",function($http, $q){

    var deferred = $q.defer();

    this.getData = function(){

        $http.get('data.json').then(function(response){
            deferred.resolve(response);
        }, function(error){
            deferred.reject(response);
        })  

        //returning the promise in the end
        return deferred.promise;
    };

});
```

The above can also be written without promises or deferred object by simple returning $http as:

```JS
angular.module("myApp",[])
.controller("myController",function($scope, myService){

    myService.getData().then(function(response){
        console.log(response);
    });
    
})
.service("myService",function($http, $q){

    this.getData = function(){

        return $http.get('data.json').then(function(response){
            return response.data;
        }, function(error){
            return error;
        })  

    };

});
```

Benefit of using promises:

1) Allows for proper handling of async calls where you can 
2) Allows you to handle multiple, nested async calls efficiently
3) Provides more usable methods to handle javascript functionality in case of async calls


## Multiple Async Call Handling Using Promises

- [Reference1](http://chariotsolutions.com/blog/post/angularjs-corner-using-promises-q-handle-asynchronous-calls/)

- [Reference2](https://github.com/kriskowal/q)
- [Reference3](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

This is done using `.all()` method in both AngularJS provided `$q` service and `Promise API` by javascript.

`Promise.all()` that resolves when all of the promises in the given iterable have resolved, or rejects if any of the promises rejects. 

If any of the passed in promises rejects, the `.all()` Promise immediately rejects with the value of the promise that rejected, ignoring all the other promises whether or not they have resolved. If an empty array is passed, then this method resolves immediately. If the returned promise fulfills, it is fulfilled with an array of the values from the fulfilled promises in the same order as defined in the iterable. If the returned promise rejects, it is rejected with the reason from the first promise in the iterable that rejected. This method can be useful for aggregating results of multiple promises.

### Note:

There is another function `.race()` which takes in an array only. It resolved whenever any one of the promises passed to it is resolved.

- [Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

Angular way:

```JS
angular.module("myApp",[])
.controller("myController",function($scope, myService){

    var urls = [
        "data0.json",
        "data1.json",
        "data2.json"
    ]

    myService.handleMultipleCalls(urls).then(function(response){
        console.log(response); //will return an array of responses
    }, function(error){
        console.log(error);
    });
    
})
.service("myService",function($http, $q){

    this.handleMultipleCalls = function(urls){

        var data = [];
        var deferred = $q.defer();
        angular.forEach(urls, function(url){
            data.push($http.get(url));
        });

        $q.all(data).then(function(response){
           deferred.resolve(response);
        }, function(error){
            deferred.reject(error);
        })

        return deferred.promise

    };

});
```

Javascript Way:

```JS
function getDataFromMultiple(urls, createXMLRequest){

    var data = []

    urls.forEach(function(url){
        data.push(
            createXMLRequest(url)
        ) 
    });

    // console.log(data);

    Promise.all(data).then(function(response){
        console.log(response);
    }, function(error){
        console.log(error);
    })

}

var urls = [
        "data0.json",
        "data1.json",
        "data2.json"
    ];

getDataFromMultiple(urls, function(url){
    var xhttp = new XMLHttpRequest();
    xhttp.open("GET",url,true);
    xhttp.send();

    return xhttp;
});
```

Fail fast approach in Promise.all.
Source: MDN

```JS
var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, 'one'); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 2000, 'two'); 
});
var p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 3000, 'three');
});
var p4 = new Promise((resolve, reject) => {
  setTimeout(resolve, 4000, 'four');
});
var p5 = new Promise((resolve, reject) => {
  reject('reject'); //this promise rejects immediately so promise overall is reject immediately
});

Promise.all([p1, p2, p3, p4, p5]).then(values => { 
  console.log(values);
}, reason => {
  console.log(reason)
});

//From console:
//"reject"

//It's even possible to use .catch
Promise.all([p1, p2, p3, p4, p5]).then(values => { 
  console.log(values);
}).catch(reason => { 
  console.log(reason)
});
```
Another simple example

```JS
var p1 = new Promise(function(resolve, reject){

    resolve("resolved");

});

p1.then(function(response){
    console.log(response);
    throw 'Oh no!'; //return Promise.reject('Oh no!') could also be used instead of throw here
}).catch(function(e){
    console.log(e);
}).then(function(){
    console.log("after the catch the chain is restored");
}, function(){
    console.log("not fired due to the catch statement");
})
```

## Use Strict

- [Reference1](http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/)
- [Reference2](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)
- [Reference3](http://cjihrig.com/blog/javascripts-strict-mode-and-why-you-should-use-it/)

Strict Mode is a new feature in ECMAScript 5 that allows you to place a program, or a function, in a "strict" operating context. This strict context prevents certain actions from being taken and throws more exceptions.

Strict mode helps out in a couple ways:

- It catches some common coding bloopers, throwing exceptions.
- It prevents, or throws errors, when relatively "unsafe" actions are taken (such as gaining access to the global object).
- It disables features that are confusing or poorly thought out.

```JS
// Non-strict code...
 
(function(){
  "use strict";
 
  // Define your library strictly...
})();
 
// Non-strict code...
```

### Development Effects:

- will not allow to declare a variable without 'var' keyword
- Any attempts to write to a property whose writable attribute is set to false, delete a property whose configurable attribute is set to false, or add a property to an object whose extensible attribute is set to false will result in an error 
- Deleting a variable, a function, or an argument will result in an error
- Defining a property more than once in an object literal will cause an exception to be thrown
- any attempts to use the name `eval` is not allowed
- Additionally, attempts to introduce new variables through an eval will be blocked
- arguments object cannot be overriden in this mode
- Identically named arguments result in an error
- arguments.caller/ arguments.callee cannot be used
- strict mode prevents null or undefined to be coerced to a global object
```JS
(function(){ ... }).call( null ); //gives exception
```

- use of with statements is prohibited
- inside an IIFE this will be undefined in strict mode
- variable definition mapping to variable name is faster in strict mode
- Sixth, strict mode in ECMAScript 5 forbids octal syntax.
- Strict mode does not allow to set properties on primitive values


```JS
'use strict';
if (true) {
  function f() { } // !!! syntax error
  f();
}

for (var i = 0; i < 5; i++) {
  function f2() { } // !!! syntax error
  f2();
}

function baz() { // kosher
  function eit() { } // also kosher
}
```


### Performance Effects:

Strict mode simplifies how variable names map to particular variable definitions in the code. Many compiler optimizations rely on the ability to say that variable X is stored in that location: this is critical to fully optimizing JavaScript code. JavaScript sometimes makes this basic mapping of name to variable definition in the code impossible to perform until runtime. Strict mode removes most cases where this happens, so the compiler can better optimize strict mode code.

### Future versions:

 in strict mode a short list of identifiers become reserved keywords. These words are implements, interface, let, package, private, protected, public, static, and yield. In strict mode, then, you can't name or use variables or arguments with these names.



## Javascript Closures

- [Reference1](https://developer.mozilla.org/en/docs/Web/JavaScript/Closures)
- [Reference2](http://javascriptissexy.com/understand-javascript-closures-with-ease/)
- [Reference3](http://stackoverflow.com/questions/12930272/javascript-closures-vs-anonymous-functions)
- [Reference4](http://stackoverflow.com/questions/2728278/what-is-a-practical-use-for-a-closure-in-javascript)
- [Reference5](https://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/)

A closure is the combination of the inner function returned by the outer executed function which has popped of from the stack and the variable that was declared local to the outer executing function which is still in memory after the outer function popped of from the stack. Therefore that variable can be accessed in the inner function that is returned.

### Application of using closures

Consequently, you can use a closure anywhere that you might normally use an object with only a single method.

- Closures are used to make function factories
- Closures are used in Module design pattern in javascript (emulating private methods with closures)
- All javascript events uses closures

## Disadvantages of using closures

It is unwise to unnecessarily create functions within other functions if closures are not needed for a particular task, as it will negatively affect script performance both in terms of processing speed and memory consumption.
For instance, when creating a new object/class, methods should normally be associated to the object's prototype rather than defined into the object constructor. The reason is that whenever the constructor is called, the methods would get reassigned (that is, for every object creation).


### Popular examples of closures found all over the web

Starting with very popular example

```JS
for (var i = 0; i < 10; i++) {
	
	setTimeout(function() {
		console.log(a)
	}, i * 1000); //prints 10, 10 times
	
}

//fix for this
for (var i = 0; i < 10; i++) {
	(function(a) {
		setTimeout(function() {
			console.log(a) //prints 0 to 9 after 1 second each
		}, i * 1000)
	})(i);
}


```
Simple example of a function factory using closure

```JS
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```


## Simple Counter Function

```JS
var counter = (function(){
	var i = 0;
	return function(){
		console.log(++i); //1 2 and so on
	}

})();

counter();
counter();

```

## Making Objects Using Constructor Function Without Newing

```JS
function Person(){

	if(this instanceof Person){
		this.firstname = "rahul";
		this.lastname = "arora";
	}else{
		return new Person();
	}

	
}

var a = new Person();

console.log(a);

var b = Person();

console.log(b);
```

<hr>

## Apply And Digest In Angular

- [Reference1](https://www.sitepoint.com/understanding-angulars-apply-digest/)
- [Reference2](http://stackoverflow.com/questions/35826219/angular-scope-digest-vs-scope-apply)

$scope.$digest() will fire watchers on the current scope, and on all of its children, too. 
$scope.$apply will evaluate passed function and run $rootScope.$digest() which will then run watchers on the entire scope object atleast twice and at max 10 times

When an error occurs in one of the watchers and you use scope.$digest, it's not handled via $exceptionHandler service, so you need to handle exception yourself. scope.$apply uses a try-catch block internally and passes all exceptions to $exceptionHandler.


```JS
angular.module("myApp", [])
.controller("myController", function($scope){

    setTimeout(function(){
        $scope.name = "rahul";
        $scope.$digest();
    }, 1000);

});
```

```JS
angular.module("myApp", [])
.controller("myController", function($scope){

    setTimeout(function(){
        $scope.$apply(function(){
            $scope.name = "rahul";    
        });
    }, 1000);

});
```

## Directive To Directive Communication

- [Reference1](https://toddmotto.com/directive-to-directive-communication-with-require/)
- [Reference2](http://stackoverflow.com/questions/20930592/whats-the-meaning-of-require-ngmodel)

Directive to directive communication can be effectively done using `require` when communicating between parent and child functionality.

`require` requires the controller functionality of the parent directive which can be accessed in the link function using $ctrl

Note: `require` takes the directive name whose controller functionality it wants to access

Due to the Angular’s compiling procedure, the controller is instantiated first, then the link function once the Directive has been compiled and linked.
Basically, the controller of one directive can be used as an API for other directives to interact with that directive

Here is the same code that uses require


```JS
angular.module("myApp", [])
.directive('tabs', function(){
    return {
        restrict: "E",
        scope: {},
        transclude: true,
        controller: function(){
            this.tabs = [];

            this.addTab = function(tab){
                this.tabs.push(tab);
            }

            this.selectTab = function(index){
                for(var i=0; i<this.tabs.length;i++){
                    this.tabs[i].selected = false;
                }
                this.tabs[index].selected = true;
            }
        },
        link: function($scope, $element, $attrs, $ctrl){
            $ctrl.selectTab($attrs.active || 0);
        },
        controllerAs: 'tabs',
        template: `
              <div class="tabs">
                <ul class="tabs__list">
                    <li ng-repeat="tab in tabs.tabs">
                        <a href="" ng-bind="tab.label" ng-click="tabs.selectTab($index);"></a>
                    </li>
                </ul>
                <div class="tabs__content" ng-transclude></div>
              </div>
            `
    };
})
.directive('tab',function(){
    return{
        restrict: 'E',
        scope: {
            label: '@'
        },
        require: '^tabs',
        transclude: true,
        template: `
          <div class="tabs__content" ng-if="tab.selected">
                <div ng-transclude></div>
          </div>
        `,
        link: function($scope, $element, $attrs, $ctrl){
            
            $scope.tab = {
                label: $scope.label,
                selected: false
            }

            $ctrl.addTab($scope.tab);
        }

    }
})
```


## Transclude And Replace

COMING SOON


## Compile And Link

- [Reference1](http://stackoverflow.com/questions/12164138/what-is-the-difference-between-compile-and-link-function-in-angularjs)
- [Reference2](http://stackoverflow.com/questions/12546945/difference-between-the-controller-link-and-compile-functions-when-definin)
- [Reference3](https://www.youtube.com/watch?v=WqmeI5fZcho)


- compile function:  used for template DOM manipulation (i.e., manipulation of tElement = template element), hence manipulations that apply to all DOM clones of the template associated with the directive. (If you also need a link function (or pre and post link functions), and you defined a compile function, the compile function must return the link function(s) because the 'link' attribute is ignored if the 'compile' attribute is defined.). It takes elem, attrs as arguments

- link function: normally use for registering listener callbacks (i.e., $watch expressions on the scope) as well as updating the DOM (i.e., manipulation of iElement = individual instance element). It is executed after the template has been cloned. E.g., inside an <li ng-repeat...>, the link function is executed after the <li> template (tElement) has been cloned (into an iElement) for that particular <li> element. A $watch allows a directive to be notified of scope property changes (a scope is associated with each instance), which allows the directive to render an updated instance value to the DOM.

- controller function - must be used when another directive needs to interact with this directive. E.g., on the AngularJS home page, the pane directive needs to add itself to the scope maintained by the tabs directive, hence the tabs directive needs to define a controller method (think API) that the pane directive can access/call. 

In general, you can put methods, $watches, etc. into either the directive's controller or link function. The controller will run first, which sometimes matters. You may want to put scope-manipulation functions inside a controller just for consistency with the rest of the framework.

`Compile function runs only once for all the instances of a directive. Link function will run once for each instance of the directive. Therefore DOM manipulation done in compile will reflect in all the instances of the directive`


## Scope Options In Directives

- [Reference1](https://www.undefinednull.com/2014/02/11/mastering-the-scope-of-a-directive-in-angularjs/)
- [Reference2](https://www.3pillarglobal.com/insights/angularjs-understanding-directive-scope)
- [Reference3](http://blogs.quovantis.com/scope-in-custom-directives-angularjs/)

By default, unless explicitly set, directives don’t create their own scope. Therefore, directives use their parent scope ( usually a controller ) as their own.


- **scope: false**

In this case the directive will use its parent scope i.e whatever template call the directive, the scope bound to the controller of that template will be used by the directive.
This scope is two way binded. Any changes made to the scope in the directive will be reflected back in the controller as well

- **scope: true**

AngularJS will create a new scope by inheriting parent scope ( usually controller scope, otherwise the application’s rootScope ). Any changes made to this new scope will not reflect back to the parent scope. 
However, since the new scope is inherited from the parent scope, any changes made in the Ctrl1 ( the parent scope ) will be reflected in the directive scope.

- **scope: {}**

Directive will get a new isolated scope.This time, there will be a new scope created for the directive, but it will not be inherited from the parent scope. This new scope also known as Isolated scope because it is completely detached from its parent scope.

In isolated scope we can pluck holes using this

```
‘@’ – Text binding / one-way binding
‘=’ – Direct model binding / two-way binding
‘&’ – Behavior binding / Method binding
```


## Optimising Your Angular Apps

- [Reference1](https://medium.com/developers-writing/tips-and-practices-to-optimize-your-angularjs-app-8118550ff808#.z7h35p48k)
- [Reference2](https://toddmotto.com/use-controller-filters-to-prevent-digest-performance-issues/)
- [Reference3]() 

AngularJS is a magic framework. Using it well will drastically improve your productivity, code modularity and reusability. Using it badly, instead, will probably end up creating messy code and a very slow application.

Some of the ways to optimize AngularJS applications are listed below:

- Avoid watchers
- Avoid $broadcast When you broadcast an event, indeed, the digest cycle goes through the scope hierarchy each time.
- Use trackby in ng-repeat. Angular does not have to rebuild an element which has already been rendered. This speeds up the cycle and avoids useless DOM manipulation.
- Use less built-in directives
- Filters are something to also be avoided as much as possible when using within a loop.
There are roughly 5 filter calls every digest loop. Imagine in an ng-repeat with 1000 items, the number of filter calls. Use $filter method inside controller instead where it is specifically required.
- '{{:: myExpression }}' can be used to bind the expression one time to the view
- If the elements to be toggle is inside a long list, where maybe expressions get re-calculated when the scope changes, you may want to have the node removed from the DOM to avoid useless calculation. However, when often toggling an element’s visibility (a loading spinner? A button?) and is not tied to a scope, ng-show/hide might be better off.
- ControllerAs Syntax can be used to organize our controller in a much better way
- use private methods, and expose to your scope only the things that are needed within your templates.
- when you need to only check the current scope (and children), you are better off using scope.$digest() than scope.$apply() as the latter will dirty-check the whole scope(internally, it runs $rootScope.digest())!
This is quite common inside directives that use, for instance, JQuery events:
- Use $cacheFactory to cache your data
- Use proper tools for debugging - ngInspector/Batarang


## Service Provider And Factory

- [Reference1](http://www.simplygoodcode.com/2015/11/the-difference-between-service-provider-and-factory-in-angularjs/)
- [Reference2](http://stackoverflow.com/questions/18939709/angularjs-when-to-use-service-instead-of-factory)

They are all providers. The factory and the service are just special cases of the provider, but you can accomplish everything you want using just provider.

Note that angular only “gets” the value once, no matter how many times the provider is injected. That means it calls $get() only once ever, stores the value provided by $get(), and gives you that same stored value every time.

```JS
var mod = angular.module("MyModule", []);

mod.provider("myProvider", function() {
    this.$get = function() {
        return "My Value";
    };
});

mod.controller("MyController", function(myProvider) {
    console.log("MyController - myProvider: " + myProvider);
});
```

Code for a factory:

```JS
mod.factory("myProvider", function() { // CHANGED “provider" to “factory"
    console.log("Factory function called.");
    return "My Value";
});

mod.controller("MyController", function(myProvider) {
    console.log("MyController - myProvider: " + myProvider);
});

mod.controller("MyController2", function(myProvider) {
    console.log("MyController2 - myProvider: " + myProvider);
});
```


Provider, factory, and service are all providers. A factory is a special case of a provider when all you need in your provider is a $get() function. It allows you to write it with less code. A service is a special case of a factory when you want to return an instance of a new object, with the same benefit of writing less code. Service function is nothing but a contructor function that returns new instance of an object but it cannot take arguments. Factory instead can `new` a function to return an object from a function constructor to which arguments can be passed.


### When to use one over the other?

Say for example you are returning an existing object defined somewhere else that takes constructor arguments. You can’t pass arguments to the service, so you would make the call with a factory instead.

```JS
mod.factory("myProvider", function() {
    console.log("Factory function called.");
    return new SomeMessageBoxClass("custom argument");
});
```

One of the main factors of deciding between a provider and factory is whether you want to be able to configure the object that is generated before it’s generated. You do this by calling module.config() and getting an instance to the provider itself (instead of the object returned by the provider). You do this by appending “Provider” to the end of your provider’s name when you are injecting it.

Example:

```JS
mod.provider("myProvider", function() {
    this.value = "My Value";

    this.setValue = function(newValue) {
        this.value = newValue;
    };

    this.$get = function() {
        return this.value;
    };
});

mod.controller("MyController", function(myProvider) {
    console.log("MyController - myProvider: " + myProvider);
});

mod.config(function(myProviderProvider) { // ADDED config section
    // Note the extra "Provider" suffix
    myProviderProvider.setValue("New Value");
});
```

There is another provider called the 'value' provider. If we just want to return a string, we can simply do it as:

```JS
mod.value("myProvider", "My Value");
```

So when would you use one versus the other? Presumably you would use the factory provider when you want to calculate the value based on some other data, for example data from another value provider or an external source. And/or when you want to calculate the value if and only when it’s first requested. Here are some examples:

```JS
// Example where factory depends on a "value" provider
mod.value("multiple", 3);
mod.factory("value", function(multiple) { 
    return 10 * multiple; 
});
// Example where factory depends on external data
mod.factory("value", function(multiple) { 
    var multiple = getDateFromExternalPage();
    return 10 * multiple; 
});
```
There is another provider called constant.

Constant is available from the configuration phase and value is not. The other difference is as the name implies you can’t change the value of a constant. The first value you assign it is the value it keeps, if you try to assign it a different value later it will be ignored.


## Config And Run

**Configuration Block**

This block is executed during the provider registration and configuration phase. We can have these as many as we want in the application. Only providers and constants can be injected into configuration blocks and not instances. This block is used to inject module wise configuration settings to prevent accidental instantiation of services before they have been fully configured. This block is created using config() method.

Configuration blocks (registered with module.config()) get executed during provider registration, and can only be injected providers and constants (see module.provider() and module.constant()). This is typically where you would configure application-wide stuff, such as the $routeProvider. Stuff that needs to be configured before the services are created.

**Run Block**

This is executed after the execution of configuration block. It inserts instances and constants but not providers. This block is created using run() method. This is similar to main method in C# or Java

You can have multiple of either, and they are executed in the order they were registered to the module. Some people prefer to register a configuration block before every group of controllers to register the routes to these controller, for example.

## Components Vs Directives

- [Reference1](http://stackoverflow.com/questions/38342664/angular-1-5-directives-vs-components)

**Advantages of Components:**

simpler configuration than plain directives
promote sane defaults and best practices
optimized for component-based architecture
writing component directives will make it easier to upgrade to Angular 2

**When not to use Components:**

for directives that rely on DOM manipulation, adding event listeners etc, because the compile and link functions are unavailable
when you need advanced directive definition options like priority, terminal, multi-element
when you want a directive that is triggered by an attribute or CSS class, rather than an element

## Angular1.x Vs Angular2

- [Reference1](https://www.quora.com/What-is-the-difference-between-AngularJs-and-Angular-2)


- With Angular 2, you can compile to native mobile code with NativeScript, deploy to mobile with Ionic and use Electron for desktop. Single codebase.

Performance improved in Angular 2.0 as compared to Angular 1.x. Bootstrap is now platform specific in angular 2.o. So if application is bootstrap from browser it will call different bootstrap as compare to mobile app. So for browser bootstrap package:angular2/platform/browser.dart is used.

For mobile loading Apache Cordova can be used to reduce loading time.

- Angular 2 is not using $scope anymore to glue view and controller. This is one of the biggest problem when you did coding in Angular 1 and then want to try Angular 2 for the project. However if anyone is coming from JAVA, .NET/ background can easily pick up because syntax are more similar to Java.

-