# Self Assesment
Questions are subsections, answers/ways for teachers to measure understanding given in quote blocks.

## 1. In your own words, describe what a Closure is
> A closure is a function within a function. Anything other than that means they missed the major point of this tutorial

## 2. Given the code block, what value would be returned by the function sayFoo?

~~~javascript
var foo = "foo";
function sayFoo(input) {
	var foo = "bar"
	return foo;
}
sayFoo(foo);
~~~
> Should answer "bar", this shows how well they understand global vs local scoping

## 3. Draw the scope chain diagram for the following code. Use the diagram from the Scope Chain section of Chapter 3 as an example.
~~~javascript
var x = 0;

function foo(t) {
	var y = 4;
	return y + t;
}

function double(num) {
	return 2 * num;
}

function bar() {
	var i = 0
	var testLst = ["a", "b", "c", "d"];
	function listLength(lst) {
		var count = 0;
		for (j = 0; j < lst.length; j += 1) {
			count += 1;
		}
		return count;
	}
}
~~~
> Question covers if they understand how scope chaining works, as it is an important aspect of closures. This shows they understand what a closure does and does not have access to.
> The diagram should look similar to the one below, making sure they write a box for the Global scope (can't forget the `x` variable)
> ![scope chain diagram](https://i.imgur.com/exjlDIn.png)

## 4. In a few sentences, explain what is wrong this anonymous closure and the subsequent function call. Then, rewrite it with the appropriate fixes
~~~javascript
var civilian = (function() {
	var stamina = -1;
	
	return {
		setStats: function (data) {
			var health = -1;
			var stamina = -1;
			health = data.health
			stamina = data.stamina
		},
	};
})();

var data = {health: 100, stamina: 100};
civilian.setStats(data);
console.log(civilian.health);
~~~
> Question covers if they understand how anonymous closures help with state and privacy management. Their answer should cover the following points:

> * Requesting private variables publically and that the console log won't return anything
> * Not keeping the stats (health, stamina) in a way such that their state will be saved

> Fixed code should adhere to the following:
~~~javascript
var civilian = (function() {
	var stamina = -1;
	var health = -1;
	return {
		setStats: function (data) {
			health = data.health
			stamina = data.stamina
		},
		getHealth: function () {
			return health;
		},
	};
})();
var data = {health: 100, stamina: 100};
civilian.setStats(data);
civilian.getHealth();
~~~