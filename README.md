# Front-end-development-Interview-Questions-with-Answers

Technologies covered

- Javascript
- Angularjs
- HTML
- CSS

## Javascript
 
- [What all design patterns do you know? Which should be used at what instance?](#design-patterns)
- [What are promises?](#design-patterns)
- [How will you handle a task that is to be done after successful completion of multiple asynchronous calls?](#design-patterns)


### Design Patterns

- Module
- Prototype
- Observer
- Singleton

### Module

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

#### Augmenting Modules

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


### Prototype

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






