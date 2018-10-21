[cs10_lecture]: https://docs.google.com/presentation/d/1zuGTzrwfTxN-dd8nS7w4sjnIJdOtc6z3GaBtu2POgiA/edit#slide=id.g12e14709f3_0_292
# What's a Closure?
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) defines a **Closure** as the following
> A closure is the combination of a function and the lexical environment within which that function was declared.

In simpler terms, a closure is a **function defined within a function**. In order to fully understand that though, we'll need a refresher from the earlier [scoping](cs10_lecture) tutorial, which we'll cover in the next chapter.

## What You Will Learn
By the end of this tutorial, you will be able to:

* Understand what a closure is and how to use it
* Understand how closures and scope are related
* Write closures of your own for characters in a text-based game
* Understand how state and privacy can be managed in JavaScript

![lets do this](https://media.giphy.com/media/zaezT79s3Ng7C/giphy.gif)
>source: Giphy

# Global vs. Local Scope
Closures rely heavily on understanding how scoping works. Remember, scoping is like your favorite vegetable/ogre:

![Onion/Ogre](https://a.wattpad.com/cover/25797160-288-k879061.jpg)
>source: Wattpad

There are **layers** of scope, and you can think of them in two main categories:

* **Global:** variables/functions that live at the *window object* level. These can be modified or used by any and all scripts
* **Local:** variables/functions that live *inside* a function, and can only be used inside the function that it lives in

~~~javascript
// This is the Global scope. All variables and functions 
// here can be accessed by all scripts.
var foo = "foo";
var bar = "bar";
var boom = "boom";

function localFn() {
	// This is the local scope to the function localFn.
	// Variables and functions created here can only be 
	// accessed by localFn
	var foo = "loFoo";
	var bar = "loBar";
	var localTest = "local";
	return foo;
}

function localFn2() {
	// This is the local scope to the function localFn2.
	// Variables and functions created here can only be 
	// accessed by localFn2
	var test = "test";
	return test;
}

~~~
## Question
[jsConsole_link]: https://jsconsole.com/
Given the above code block, answer the following questions:

1. What value would be returned if we ran `localFn()`?
2. If we changed the `return` value of `localFn()` to `boom`, what would be returned?
3. If we changed the `return` value of `localFn2()` to `localTest`, what would be returned?

Try them for yourself by running them in [jsConsole][jsConsole_link]!

# Closures
## Recap
* Global variables/functions can be accessed by all other variables and functions
* Local variables/functions can only be accessed within the function they live in

This is why in question 1 from the last chapter, `localFn()` returned `localfoo` and not `foo`, since there was a `foo` variable that lived within its own scope, it didn't look further to check out the global variables. **If variables have the same name, the most local variable will always have the highest priority**.

This is also why in question 3 from the last chapter, `localFn2()` returned a reference error, since `localTest` was not a variable that was wihin the local or global scope of `localFn()`.

## Closures and....Spider-Man?
The new Spider-Man game just launched last month! As an avid Spider-Man fan, you were inspired by playing the game: how natural web-swinging felt, how fluid the combat system was, and those stunning 4k graphics.

![Spider-Man](http://snappi-wpengine.netdna-ssl.com/wp-content/uploads/2018/08/Spider-Man-PS4.jpg)
> Source: TakesOnTech

Overcome with inspiration, you're want to make Spider-Text, a text-base Spider-Man game, so that you can get more practice programming with a topic you love!

## Web-Slinger
The first thing we'd want to do is build out some of Spider-Man's attributes. Before we build out everything, let's just cover one basic trait: he needs to be able to swing on his webs!

We need to write a function for him to be able to swing, but in order to swing, he needs to be able to shoot webs.

Remember, this is a much simpler Spider-Man, and he's only ever going to shoot webs if he's swinging. We don't want him to be able to shoot webs at any other time, so we can't make his web shooting function available in the global scope.

But what if we defined a function ***within*** a function?

~~~javascript
// can shoot 100 times before needing to refill
var webCounter = 100;

function swingJump() {
	// code that automatically handles Spider-Man's jumping while swinging...
}

// Checks how much web spider-man has left to use, returns -1 if he's out
function checkWebSupply() {
	if (webCounter <= 0) {
		return -1;
	}
	return webCounter;
}

// Returns an random integer between 0 and max
function getRandomInt(max) {
  return Math.floor(Math.random() * Math.floor(max));
}

// Spider-man says a quip, shoots his web, and then swings from it
function webSwing() {
	// Spider-Man is a talker, so we need some phrases for him to say
	var quips = ["just hangin", "spin a web any size", "look ma, no hands!"]
	
	function shootWeb() {
		webCount = checkWebSupply();
		if (webCount <= 0) {
			return "I'm out of webs!";
		}
		webCounter -= 1
		return quips[getRandomInt(quips.length)];
	}
	swingJump();
	return shootWeb() + " - I'm swinging!";
}
webSwing(); // webCounter = 99, spidey says "spin a web any size - I'm swinging!"
webSwing(); // webCounter = 98, spidey says "just hangin - I'm swinging!"
~~~

`shootWeb()` is able to access the variables from its parent function `webSwing()`! Because the innermost (child) function *lives inside the parent function*, it is able to access it's parent's scope. This is an example of **lexical scoping**.

**This overall concept of defining a function within a function is a closure!**

## Scope Chain
By declaring functions within functions, you create a **Scope Chain**. A scope chain is a one-way scoping relationship where a function has access to all scopes above it in the chain.

![global to parent to child)](https://i.imgur.com/ppkqlvu.png)
> Source: Ian Birnam

Every time we add a new function, a new link to the chain is added. The parent could have another child, there could be a grand-child link, or a great-grand-child, there's no limit!

## Question
Spider-Man needs to be able to punch the bad guys like Venom and Electro, but he also can only fight for so long before he gets tired. Write functions to meet the following requirements:

* The `punch()` and `kick()` functions live within a parent `fight()` function, which randomly returns a punch or a kick
* A punch decreases his `stamina` by 1
* A kick decreases his `stamina` by 2
* Punches should return a string of `"spidey punched!"`
* Kicks should return a string of `"spidey kicked!"`
* If he runs out of stamina, return a string of `"gotta run!"`

### Tests
Your code should pass the following tests:

* `fight()` should return a string of `"spidey [punched][kicked]!"` depending on which one got selected, and `stamina` should decrement accordingly.
* If `fight()` is called when `stamina <= 0`, it should return a string of `"gotta run!"`

Verify and test your code with [jsConsole][jsConsole_link], and then push it to Github when you're done!

# Anonymous Closures
## Recap
* A Closure is a function defined within a function
* Scope chain shows how scope is passed on from outer to inner (parent to child) functions
	* Chain always goes *one way*, a parent will *never* have access to the scope of its child

## Spider-Man the Variable
So we made some functions for Spider-Man to do things, but how can we web swing or fight Green Goblin if we don't have an actual Spider-Man to do that? Spider-Text requires a Spider-Man character! As the game's creater, you want to have a Spider-Man character so that you can make use of the functions you've already written. We should write Spider-Man as a closure!

Let's go over what needs to be captured in this character:

* He needs to have his secret identity: Peter Parker
* He needs to be able to keep track of how much webbing he still has
* He needs to track his health
* He needs to track his stamina
* He needs to be able to fight
* He needs to be able to swing from webs

The above requirements aren't *all* the requirements of Spider-Man, but we need to start somewhere. We'll tackle them in sections:

### Secret Identity, Health, Stamina, and Webbing
These should be variables of the Spider-Man character

~~~javascript
function() {
	//prviate variables
	var secretIdentity = "";
	var health = -1;
	var stamina = -1;
	var webCounter = -1;
}
~~~

### Init
We need to be able to initialize these variables when we first create our Spider-Man character, so we should make an initialize function:

~~~javascript
function() {
	//prviate variables
	var secretIdentity = "";
	var health = -1;
	var stamina = -1;
	var webCounter = -1;
	
	//private functions

	// Initializes Spider-Man's data
	function init(data) {
		secretIdentity = data.secretIdentity;
		health = data.health;
		webCounter = data.webCounter;
		stamina = data.stamina;
	}
}
~~~

### Swing and Fight
We want Spider-Man to be able to swing and fight, and we want anyone to be able to call those functions when they want to, so we need to return them:

~~~javascript
function() {
	//prviate variables
	var secretIdentity = "";
	var health = -1;
	var stamina = -1;
	var webCounter = -1;
	
	//private functions
	
	// Initializes Spider-Man's data
	function init(data) {
		secretIdentity = data.secretIdentity;
		health = data.health;
		webCounter = data.webCounter;
		stamina = data.stamina;
	}
	
	// Checks how much web spider-man has left to use, returns -1 if he's out
	function checkWebSupply() {
		if (webCounter <= 0) {
			return -1;
		}
		return webCounter;
	}

	// Returns an random integer between 0 and max
	function getRandomInt(max) {
	  return Math.floor(Math.random() * Math.floor(max));
	}
	
	function swingJump() {
		// code that automatically handles Spider-Man's jumping while swinging...
	}
	
	// any other private functions you need for the fight function...
	
	//public functions
	return {
		setStats: function (data) {
			return init(data)
		},
		getHealth: function () {
			return health;
		},
		fight: function () {
			//fight code from previous chapter...
		},
		webSwing: function() {
			// Spider-Man is a talker, so we need some phrases for him to say
			var quips = ["just hangin", "spin a web any size", "look ma, no hands!"]
			var shootWeb = function() {
					webCount = checkWebSupply();
					if (webCount <= 0) {
						return "I'm out of webs!";
					}
					webCounter -= 1
					return quips[getRandomInt(quips.length)];
				};
			swingJump();
			return shootWeb() + " - I'm swinging!";
		}
	}
};
var data = {secretIdentity: "Peter Parker", health: 100, webCounter: 100, stamina: 100};

setStats(data); // secretIdentity, health, webCounter all initialized
getHealth(); // returns 100
webSwing(); // webCounter down to 99, spidey says "spin a web any size - I'm swinging!"
~~~

This seems good, but there's still a big problem with our code. We can't call any of the functions we wrote, as we did not store them anywhere!

## Function Declaration Vs. Expression
There's also the problem that we've **declared** a large, Spider-Man function, but declaring it won't allow us to do anything with it. a **Function Declaration** is when we write something in the style of `function () {...}`

Instead, we need to write a **Function Expression**, which is when when we store a function as a variable such as ` var spiderMan = function() {...};`

The main difference between the two is that **Function Expressions can retain a copy of the local variables/functions from the scope in which they were defined in.**

By storing the function in a variable, we will be able to create **state** for our character. State will allow us to save Spider-Man's data (like his secret identity), and allows us to update data and have those updates persist.

Let's store our big function into a `var spiderMan`:

~~~javascript
var spiderMan = function() {
	// All code that existed within the function declaration from the last code block...
};
var data = {secretIdentity: "Peter Parker", health: 100, webCounter: 100, stamina: 100};

setStats(data); // secretIdentity, health, webCounter all initialized
getHealth(); // returns 100
webSwing(); // webCounter down to 99, spidey says "spin a web any size- I'm swinging!"
~~~
That should get us what we want...right?
## Anonymous Closures
Just making a function expression isn't going to be enough. There's still one more missing piece we need to go over.

Currently, we are trying to make function calls on a function expression that has not executed yet, and therefore will give us errors (try running the code yourself to see).

What we need to do is create an **Anonymous Closure**, also known as an **Instantly Invoked Function Expression (IFFE)**. We create these by putting parenthesis around our `spiderMan` function, followed by calling it:

~~~javascript
var spiderMan = (function() {...})();
~~~

Anonymous closures also allow us have private variables/functions in JavaScript, as there is no way to access variables or functions unless you return them, since we've restricted the scope.

Now we can finally create our Spider-Man:

~~~javascript
var spiderMan = (function() {
	//prviate variables
	var secretIdentity = "";
	var health = -1;
	var stamina = -1;
	var webCounter = -1;
	
	//private functions
	
	// Initializes Spider-Man's data
	function init(data) {
		secretIdentity = data.secretIdentity;
		health = data.health;
		webCounter = data.webCounter;
		stamina = data.stamina;
	}
	
	// Checks how much web spider-man has left to use, returns -1 if he's out
	function checkWebSupply() {
		if (webCounter <= 0) {
			return -1;
		}
		return webCounter;
	}
	
	// Returns an random integer between 0 and max
	function getRandomInt(max) {
	  return Math.floor(Math.random() * Math.floor(max));
	}
	
	function swingJump() {
		// code that automatically handles Spider-Man's jumping while swinging...
	}
	
	// any other private functions you need for the fight function...
	
	//public functions
	return {
		setStats: function (data) {
			return init(data)
		},
		getHealth: function () {
			return health;
		},
		fight: function () {
			//fight code from previous chapter...
		},
		webSwing: function() {
			// Spider-Man is a talker, so we need some phrases for him to say
			var quips = ["just hangin", "spin a web any size", "look ma, no hands!"]
			var shootWeb = function() {
					webCount = checkWebSupply();
					if (webCount <= 0) {
						return "I'm out of webs!";
					}
					webCounter -= 1
					return quips[getRandomInt(quips.length)];
			};
			swingJump();
			return shootWeb() + " - I'm swinging!";
		}
	};
})();

var data = {secretIdentity: "Peter Parker", health: 100, webCounter: 100, stamina: 100};
spiderMan.setStats(data); // secretIdentity, health, webCounter all initialized
spiderMan.getHealth(); // returns 100
spiderMan.webSwing(); // webCounter down to 99, spidey says "spin a web any size - I'm swinging!"
~~~
## Question
Now that we have Spider-Man, we need to make a bad guy to chase after him. Let's create a basic Electro given the following requirements:

* Electro has to have secret identity, health, stamina, and charge level stats
* Electro should have a `fight` function that randomly picks between a punch, kick, or electric shock
	* punch requires 1 stamina
	* kick requires 2 stamina
	* electric shock requires 3 stamina and 5 charge
* Electro can also fire off an electric storm at will, which requires 10 stamina and 15 charge
* If the `fight()` or `storm()` functions are called and `stamina <= 0`, Electro should say `"I need more juice!"`
* There needs to be a public way to set Electro's stats
* There needs to be a public way to individually get Electro's health, stamina, and charge level stats
* There needs to be a public way to make Electro fight
* There needs to be a public way to make Electro fire off an electric storm

### Tests
Your code should pass the following tests:

* `electro.init(data)` should take in an object with name/value pairs for `health`, `stamina`, `charge`, and `secretIdentity`
* `electro.getHealth()`, `electro.getStamina()`, and `electro.getCharge()` should all return an integer that corresponds to electro's health, stamina, and charge respectively
* `electro.fight()` should return a string of `"electro [punched][kicked][shocked]!"` depending on which one got selected, and the appropriate stamina and charge stats should decrement accordingly.
* `electro.storm()` should return a string of `"electro launched a storm!"`, and the appropriate stamina and charge stats should decrement accordingly
* If the `fight()` or `storm()` functions are called and `stamina` is <= 0, return a string of `"I need more juice!"`

Verify and test your code with [jsConsole][jsConsole_link], and then push it to Github when you're done!

# Conclusion
We've got our hero and villian for Spider-Text, what else can we build out? Can we interact with civillians? How do characters take damage? Can we allow Spider-Man to crawl on walls?  Continue to think about how you can build out Spider-Text, and if closures can help you to do so!

While Spider-Text is amazing, it showcases how closures are a phenominal example of JavaScript's versitility. We're able to nest functions within each other, define clear scope boundaries, set functions to variables, create anonymous functions, and self-invoke them as well. 

Closures also allows for [object-oriented programming](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS) in JavaScript, in addition to many other more advanced techniques that we'll cover in later tutorials. What are some other applications of closures you can think of?

Please check out the [self assessment questions](self-assessment.md) to see how well you've mastered the concepts!

When you think about closures, remember:

* They're functions within functions
* The scope of variables/functions is a one-way chain from parent to child
* Anonymous closures execute immediately, and allow us to maintain state and privacy

![Closure](https://media.giphy.com/media/yWli7pIg5myys/giphy.gif)
> Source: Giphy