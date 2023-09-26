## useState
- 效果：向组件添加状态变量
- 语法：`useState(initialState)`
- 参数
	- `initialState`：初始值，或初始化函数
- 返回值
	- `state`：当前状态
	- `setState`：set函数，更新下次渲染时的 `state` 值，若值不同，则触发重渲染
		- `nextState`：新的值，或更新函数
1. 初始值
```jsx
const [age, setAge] = useState(18)
setAge(20) // 更新age
console.log(age) // 20
```
2. 对象初始值
因为 `setState` 会覆盖之前的值，所以更新对象时，需要结合旧的值
```jsx
const [info, setInfo] = useState({name: 'xxc', age: 18})
setInfo({name: 'xxc', age: 20}) // 更新整个对象
console.log(info.age) // 20

setInfo({...info, age: 10}) // 解构
console.log(info.age) // 10
```
3. 初始化函数
因为函数组件每次渲染会重新执行，`initialState` 会被重新计算，所以很慢，而使用初始化函数，则仅在第一次渲染时计算状态
```jsx
// ✅ 传递函数本身，仅在第一次渲染执行
const [state, setState] = useState(init)

// ❌ 传递函数计算结果，每次渲染都执行
const [state, setState] = useState(init())
```
4. 更新函数
更新函数可以取到前一个状态的值，表示为 `s => s + 1`，参数 `s` 为 `pending state`
```jsx
// 传入值
const [age, setAge] = useState(18)
setAge(age + 1) // setAge(18 + 1)
setAge(age + 1) // setAge(18 + 1)

// 传入更新函数
const [num, setNum] = useState(18)
setNum(prevNum => prevNum + 1) // setNum(18 => 19)
setNum(prevNum => prevNum + 1) // setNum(19 => 20)
```
## useReducer
- 效果：管理复杂状态
- 语法：`useReducer(reducer, initialArg, init?)`
- 参数
	- `reducer`：用来更新state函数
		- `state`：当前状态
		- `action`：用户行为
	- `initialArg`：state的初始值
	- `init?`：初始化函数，指定后，初始值被设置为 `init(initialArg)`
- 返回值
	- `state`：当前状态
	- `dispatch`：更新state的函数
		- `action`：用户行为，将会传递到reducer
```jsx
export default function Man() {
	const [state, dispatch] = useReducer(reducer, {name: 'xxc', age: 23})
	const handleClick = () => {dispatch({type: 'addAge'})} // dispatch function
	return <button onClick={handleClick}>{name} {age}</button>
}

function reducer(state, action) { // reducer function
	switch (action.type) {  
		case 'addAge': {
			return {...state, age: state.age + 1}
		}
	} 
}
```
## useRef
- 效果：引用一个不需要渲染的值，在多次渲染间保存
- 语法：`useRef(initialValue)`
- 参数
	- `initialValue`：初始值
- 返回值：`{ current: initialValue }` 对象，修改他不会触发重渲染
1. 引用值
```jsx
const ref = useRef(0)
alert(ref.current)  // 0
```
2. 引用DOM
```jsx
function Input() {
	const ref = useRef(null)
	ref.current.value = '' // 清空输入框
	return <input ref={ref}> {/* 绑定ref到<input> */}
}
```
3. 储存PrevState的值
```jsx
function Input() {
	const [name, setName] = useState('xxc')
	const prevNameRef = useRef(null)
	useEffect(() => {
		prevNameRef.current = name // 保存之前的状态
	})
}
```
::: danger
不要在渲染期间读写 `ref.current`，可以在 `useEffect`, `事件处理函数` 中读写
:::
## useImperativeHandle
- 效果：自定义 `ref` 的值
- 语法：`useImperativeHandle(ref, createHandle, dependencies?)`
- 参数
	- `ref`：父组件传递的 `ref`
	- `createHandle`：一个函数，返回 `ref` 的新值
	- `dependencies?`：依赖数组
- 返回值：`undefined`
1. 向父组件暴露方法
```jsx
// 子组件
const Panel = forwardRef((props, ref) => {
	const [num, setNum] = useState(0)
	useImperativeHandle(ref, () => { // 将ref指向对象{ add, addX }
		return {
			add() {setNum(num + 1)}, // 暴露add()方法
			addX(x) {setNum(num + x}, // 暴露addX()方法
		}
	})
	return <h1>{num}</h1>
})
```
## useContext
- 效果：读取 `<Provider>` 中的 context
- 语法：`useContext(SomeContext)`
- 参数
	- `SomeContext`：之前用 `createContext` 创建的 context
- 返回值：调用组件上方最近的 `SomeContext.Provider` 的 `value` 值，若没找到，则返回创建时的默认值。`value` 改变会触发重渲染
1. 向所有子组件传值
```jsx
const ThemeContext = createContext(null) // 创建context，默认值null

export default function Page() {
	return (
		<ThemeContext.Provider value="dark"> {/* 提供context，传递值'dark' */}
			<Button />
		</ThemeContext.Provider>
	)
}

function Button() {
	const theme = useContext(ThemeContext) // 获取context的值'dark'
}
```
2. 传递更新函数
```jsx
const ThemeContext = createContext(null) // 创建context，默认值null

export default function Page() {
	const [theme, setTheme] = useState('dark')
	return (
		<ThemeContext.Provider value={{ theme, setTheme }}> {/* 提供context，传递对象 */}
			<Button />
		</ThemeContext.Provider>
	)
}

function Button() {
	const { theme, setTheme } = useContext(ThemeContext) // 获取context的对象
}
```
3. 抽离Provider
## useEffect
- 效果：它允许你将组件与外部系统(DOM, API)同步
- 语法：`useEffect(setup, dependencies?)`
- 参数
	- `setup`：每次渲染后执行的函数，返回 `clear` 函数
	- `dependencies?`：依赖数组
- 返回值：`undefined`
1. 连接服务器
```jsx
export default function Counter() {
	const [url, setUrl] = useState('xxc.com');
  
	useEffect(() ={
		const connection = createConnection(url)
		connection.connect()
		return () => {connection.disconnect()} // 返回clear函数
	}, [url])
}
```
2. dependencies 参数
```jsx
// 缺省
useEffect(() => {...}) // 每次渲染都运行
// []
useEffect(() => {...}, []) // 仅在组件挂载时运行
// [dep]
useEffect(() => {...}, [dep]) // 依赖变化时运行
```
3. clear 函数
```jsx
useEffect(() => {
  console.log("effect")
  return () => {console.log("clean")} // 返回clear函数
})

// 组件挂载，运行setup
// effect

// 重新渲染，使用旧的props和state运行clearup，使用新的props和state运行setup
// clean
// effect

// 组件卸载，运行clearup
// clean
```
## useMemo
- 效果：在每次重新渲染时，缓存函数结果，是一种性能优化手段
- 语法：`useMemo(calculateValue, dependencies)`
- 参数
	- `calculateValue`：需要缓存计算值的函数
	- `dependencies`：依赖数组
- 返回值：`calculateValue` 的结果
1. 跳过重复计算
```js
function App({ num }) {  
	const out = slowFunction(num) // 每次渲染，都会重复计算
	const out = useMemo(() => slowFunction(num), [num]) // 当num不变时，将会使用缓存值
}
```
2. 引用一致
```jsx
function App() {
	const xxc = {age: 18} // 每次渲染，xxc都是不同的对象
	const xxc = useMemo(() => ({age: 18}), [age]) // 当age不变时，xxc总是同一个对象
}
```
## useCallback
- 效果：在每次重新渲染时，缓存函数本身，是一种性能优化手段
- 语法：`useCallback(fn, dependencies)`
- 参数
	- `fn`：需要缓存的函数
	- `dependencies`：依赖数组
- 返回值：`fn` 函数
1. 引用一致
```jsx
function APP({ num }) {
	const getNum = () => {num + 1} // 每次渲染，getNum都是不同的函数
	const getNum = useCallback(() => {num + 1}, [num]) // 当num不变时，getNum总是相同的函数
}
```
::: danger
在 JS 中，`function() {}` 或 `() => {}` 总是会生成不同的函数，`{}` 会生成不同的对象
:::
