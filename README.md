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


