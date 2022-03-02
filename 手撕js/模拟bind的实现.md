> Type: #Note 
> Date: 2022-03-01
> Related: 
> Tags: #笔试题 
> Reference: 

----
```js
Function.prototype.bind = Function.prototype.bind || function(context){
	if(typeof this !== 'function'){
		throw new Error("can't bind");
	}

	let self = this;
	let args = Array.prototype.slice(arguments, 1); // 缓存参数

	const fnOP = function(){};

	const bound = function(){
		let innerArgs = Array.prototype.slice(arguments);

		return self.apply(this instanceof fnOP ? this : context, args.concat(innerArgs));
	}

	fnOP.prototype = this.prototype;
	bound.prototype = new fnOP();

	return bound;
}
```

```js
// ES6版本
Function.prototype.bind = Function.prototype.bind || function(){
	if(typeof this !== 'function'){
		throw new Error("can't bind");
	}

	const args = Array.from(arguments);
	const context = args.shift();
	const self = this;

	const fnOP = Object.create(this.prototype);

	const fn = function(){
		const innerArgs = Array.from(arguments);
		return self.apply(this instanceof fnOP ? this : context, args.concat(innerArgs))
	}

	fn.prototype = fnOP;

	return fn;
}
```