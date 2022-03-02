> Type: #Note 
> Date: 2022-02-28
> Related: 
> Tags: #笔试题
> Reference: 

----

## 题意
```js
// 使用前

fs.readFile("./index.js", (err, data) => {

	if (!err) {
	
	  console.log(data.toString());
	
	}

	console.log(err);

});

// 使用promisify后

const readFile = promisify(fs.readFile);

readFile("./index.js")

	.then((data) => {
	
	  console.log(data.toString());
	
	})
	
	.catch((err) => {
	
	  console.log("error:", err);
	
	});
```

## 解法
```js
function promisify(fn){

	return (...args) => {
		return new Promise((resolve, reject) => {
			fn.apply(this, [...args, (err, data) =>{
				if(!err){
					return resolve(data);
				}

				return reject(err);
			}])
		})
	}
}
```