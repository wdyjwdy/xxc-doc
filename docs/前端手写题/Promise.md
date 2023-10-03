## Todo
- 状态从 `pending` 转化为 `fulfilled` 或 `rejected` 之后，不能再变化
- 处理 `then` 的同步回调
- 处理 `then` 的异步回调（`setTimeout`）
- 处理 `then` 的链式调用
- 处理 `then` 的微任务

:::danger 为了简洁，省略了一些错误判断：
1. `promise` 出错时必须 `reject`
2. `then()` 的参数必须为函数
3. `all()` 的参数必须可迭代
4. `any()` 全拒绝，应 `reject AggregateError`
:::
## 结构
```js
class MyPromise {
	constructor(executor) {
		this.state = 'pending'
		this.result = undefined
		this.onFulfilledCallbacks = []
		this.onRejectedCallbacks = []
		
		const resolve = () => {}
		const reject = () => {}
		
		executor(resolve, reject)
	}
	
	then() {}
	catch() {}
	finally() {}
	static all() {}
	static any() {}
	static allSettled() {}
	static race() {}
}
```
## constructor()
```js
constructor(executor) {
	this.state = 'pending'
	this.result = undefined
	this.onFulfilledCallbacks = []
	this.onRejectedCallbacks = []
	
	const resolve = value => {
		queueMicrotask(() => { // 处理微任务
			if (this.state === 'pending') { // 处理状态单次变化
				this.state = 'fulfilled'
				this.result = value
				this.onFulfilledCallbacks.forEach(cb => cb(this.result))
			}
		})
	}
	
	const reject = value => {
		queueMicrotask(() => {
			if (this.state === 'pending') {
				this.state = 'rejected'
				this.result = value
				this.onRejectedCallbacks.forEach(cb => cb(this.result))
			}
		})
	}
	
	executor(resolve, reject)
}
```
## then()
```js
then(onFulfilled, onRejected) {
	return new MyPromise((resolve, reject) => { // 处理链式调用
		const resolvePromise = result => {
			if (result instanceof MyPromise) {result.then(resolve, reject)}
			else {resolve(result)}
		}

		if (this.state === 'fulfilled') {resolvePromise(onFulfilled(this.result))} // 处理同步回调
		if (this.state === 'rejected') {resolvePromise(onRejected(this.result))}
		if (this.state === 'pending') {
			this.onFulfilledCallbacks.push(value => resolvePromise(onFulfilled(value))) // 处理异步回调
			this.onRejectedCallbacks.push(value => resolvePromise(onRejected(value)))
		}
	})
}
```

## catch()
```js
catch(onRejected) {
	const onFulfilled = value => value
	return this.then(onFulfilled, onRejected)
}
```
## finally()
```js
finally(onFinally) {
	const onFulfilled = value => {
		onFinally()
		return value
	}
	return this.then(onFulfilled, onFulfilled)
}
```
## all()
```js
static all(promises) {
	return new MyPromise((resolve, reject) => {
		let result = []
		
		const onFulfilled = value => {
			result.push(value)
			if (result.length === promises.length) {resolve(result)}
		} 
		
		const onRejected = value => {reject(value)}
		
		promises.forEach(promise => {
			if (promise instanceof MyPromise) {promise.then(onFulfilled, onRejected)}
			else {onFulfilled(promise)}
		})
	})
}
```
## any()
```js
static any(promises) {
	return new MyPromise((resolve, reject) => {
		let result = []
		
		const onFulfilled = value => {resolve(value)}
		
		const onRejected = value => {
			result.push(value)
			if (result.length === promises.length) {reject(result)}
		}
		
		promises.forEach(promise => {
			if (promise instanceof MyPromise) {promise.then(onFulfilled, onRejected)}
			else {onFulfilled(promise)}
		})
	})
}
```
## race()
```js
static race(promises) {
	return new MyPromise((resolve, reject) => {
		const onFulfilled = value => resolve(value)
		const onRejected = value => reject(value)
		
		promises.forEach(promise => {
			if (promise instanceof MyPromise) {promise.then(onFulfilled, onRejected)}
			else {onFulfilled(promise)}
		})
	})
}
```
## allSettled()
```js
static allSettled(promises) {
	return new MyPromise((resolve, reject) => {
		let result = []
		
		const onFulfilled = value => {
			result.push({ state: 'fulfilled', result: value })
			if (result.length === promises.length) {resolve(result)}
		}
		
		const onRejected = value => {
			result.push({ state: 'rejected', result: value })
			if (result.length === promises.length) {resolve(result)}
		}
		
		promises.forEach(promise => {
			if (promise instanceof MyPromise) {promise.then(onFulfilled, onRejected)}
			else {onFulfilled(promise)}
		})
	})
}
```