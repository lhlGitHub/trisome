> Type: #Note 
> Date: 2022-03-01
> Related: 
> Tags: #笔试题 
> Reference: 

----
```js
function Otaku(name, age) {
	this.name = name;
	this.age = age;
	
	this.habit = 'Games';
}

Otaku.prototype.strength = 60;

Otaku.prototype.sayYourName = function () {
	console.log('I am ' + this.name);
}

// 调用
myNew(Otaku, 'Kevin', '18');

console.log(person.name) // Kevin
console.log(person.habit) // Games
console.log(person.strength) // 60

person.sayYourName(); // I am Kevin
```

```js
// 实现
function myNew(fn, ...args){
	const obj = Object.create(fn.prototype);

	const ret = fn.apply(obj, args); // ret有可能为null

	return typeof ret === 'object' ? ret || obj : obj;
}
```
