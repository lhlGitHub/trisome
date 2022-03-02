> Type: #Note 
> Date: 2022-02-28
> Related: 
> Tags: #笔试题 
> Reference: 

----

## promise.first
> 第一个返回成功的Promise

```js
if(!Promise.first){
	Promise.first = promiseList => {
		return new Promise((resolve, reject) => {
			let rejectNum = 0;
			const len = promiseList.length;

			promiseList.forEach((pr) => {
				Promise.resolve(pr)
					.then(resolve).catch(() => {
						rejectNum++; // 记录reject的个数
						if(rejectNum == len){
							reject("all promise is reject");
						}
					})
			})
		})
	}
}
```

## promise.last
> 最后一个返回成功的Promise

```js
if(!Promise.last){
	Promise.last = promiseList => {
		return new Promise((resolve, reject) => {
			let num = 0;
			const len = promiseList.length;
			let lastResult;

			const fn = () => {
				if(num !== len){
					return;
				}
				lastResult ? resolve(lastResult) : reject("all promise is reject");
			}

			promiseList.forEach((pr) => {
				Promise.resolve(pr).then((data) => {
					lastResult = data;
					num++;
					fn();
				}).catch(() => {
					num++;
					fn();
				})
			})
		})
	}
}
```

## promise.none

> 与Promise.all相反，所有的promise都被拒绝，则Promise.none变成完成状态

```js
if(!Promise.none){
	Promise.none = (promiseList) => {
		return Promise.all(promiseList.map(pr => {
			return new Promise((resolve, reject) => {
				return Promise.resolve(pr).then(reject, resolve);
			})
		}))
	}
}
```

## Promise.any
> 表示只获取所有的promise中进入完成状态的结果，被拒绝的则忽略掉

```js
if(!Promise.any){
	Promise.any = (promiseList) => {
		return new Promise((resolve, reject) => {
			const result = [];
			let num = 0;
			const len = promiseList.length;

			const fn = () => {
				if(num == len){
					result.length < 1 ? reject("all promise is reject") : resolve(result);
				}
			}

			promiseList.forEach(pr => {
				Promise.resolve(pr).then((data) => {
					result.push(data);
					num++;
					fn();
				}).catch(() => {
					num++;
					fn();
				})
			})
		})		
	}
}
```

## Promise.every
> 所有promise都进入完成状态，则返回true，否则返回false

```js
if(!Promise.every){
	Promise.every = promiseList => {
		return new Promise((resolve, reject) => {
			Promise.all(promiseList).then(() => Promise.resolve(true))
				.catch(() =>  Promise.reject(false));
		})
	};
}
```