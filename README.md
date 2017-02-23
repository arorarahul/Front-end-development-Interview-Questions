# Front-end-development-Interview-Questions-with-Answers

Technologies covered

- Javascript
- Angularjs
- HTML
- CSS

## Javascript
 
- [What all design patterns do you know? Which should be used at what instance?](#design-patterns)


### Design Patterns

- Module
- Prototype
- Observer
- Singleton

#### Module

Used for keeping particular pieces of code independent of other components.
Modules are nothing but classes that provide encapsulation protecting behaviour and states from being accessed from other classes. Modules should be an IIFE and should return an object

```JS
(function(){
	
	//delcare private variables and functions here

	return {
		//declare public variables and functions
	}

})();
```


