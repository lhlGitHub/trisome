> Type: #Note 
> Date: 2022-03-01
> Related: 
> Tags: #笔试题 
> Reference: 

----

## Promise要点
- Promise是一个类，传入的参数是一个执行器，会立即执行
- Promise有三种状态
	- pending 等待
	- fulfilled 完成
	- rejected 失败
- 状态只能改变一次，不能逆向。
	- pending -> fulfilled
	- pending -> rejected
- Promise使用resolve和reject两个函数来改变状态
- then方法内部做状态判断，执行对应的方法
- 有静态方法Promise.resolve和Promise.reject

## Promise手写版

```js
// 状态常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class Promise{
	state = PENDING;
	value = undefined;
	reason = undefined;
	onResolveCallback = [];
	onRejectCallback = [];

	constructor(executor){

		try{
			executor(this.resolve, this.reject);
		}catch(err){
			this.reject(err);
		}
		
	}
	resolve(data){
		if(this.state !== PENDING){
			return;
		}
		this.state = FULFILLED;
		this.value = data;

		while(this.onResolveCallback.length > 0){
			const currentResolve = this.onResolveCallback.shift();
			currentResolve(this.value);
		}
	}
	reject(err){
		if(this.state !== PENDING){
			return;
		}
		this.state = REJECTED;
		this.reason = err;

		while(this.onRejectCallback.length > 0){
			const currentReject = this.onRejectCallback.shift();
			currentReject(this.reason);
		}
	}
	static resolve(param){
		// param是Promise对象，直接返回
		if(param instanceof Promise){
			return param;
		}

		// 转成普通的Promise
		return new Promise(resolve => {
			resolve(param);
		})
	}
	static reject(reason){
		return new Promise((resolve, reject) => {
			reject(reason);
		})
	}
	catch(fn){
		if(this.state == REJECTED){
			typeof fn == 'function' && fn(this.reason);
		}
	}
	then(resolve, reject){
		const realResolve = typeof resolve == 'function' ? resolve : value => value;
		const realReject = typeof reject == 'function' ? reject : reason => { throw reason };

		// 链式调用，需要返回新的Promise实例

		const newPromise = new Promise((resolve, reject) => {
			// 创建一个微任务，等待Promise初始化完成

			const microResolve = () => {
				queueMicrotask(() => {
					try{
						const x = realResolve(this.value);
						resolvePromise(newPromise, x, resolve, reject);
					}catch(err){
						reject(err);
					}
				})
			};

			const microReject = () => {
				queueMicrotask(() => {
					try{
						const x = realReject(this.reason);
						resolvePromise(newPromise, x, reasolve, reject);
					}catch(err){
						reject(err);
					}
				});
			};
		

			if(this.state == FULFILLED){
				return microResolve(this.value);
			}
			if(this.state == REJECTED){
				return microReject(this.reason);
			}
			if(this.state == PENDING){
				this.onResolveCallback.push(microResolve);
				this.onRejectCallback.push(microReject);
			}
			
		});

		return newPromise;
		
	}
}

function resolvePromise(newPromise, x, resolve, reject){
	if(newPromise == x){
		return reject(new Error('循环引用'));
	}

	if(x instanceof Promise){
		x.then(resolve, reject);
	}else{
		resolve(x);
	}
}
```