## createContext
- 效果：创建一个上下文
- 语法：`createContext(defaultValue)`
- 参数
	- `defaultValue`：上下文的默认值
- 返回值：对象
1. 使用context
```jsx
const ThemeContext = createContext(null) // 创建context，默认值null

export default function Page() {
	return (
		{/* 提供context，传递值'dark' */}
		<ThemeContext.Provider value="dark">
			<Button />
		</ThemeContext.Provider>
	)
}  
function Button() {
	const theme = useContext(ThemeContext) // 使用context的值'dark'
}
```
2. 导入导出
```jsx
// Contexts.js  
export const ThemeContext = createContext('light') // 导出

// Button.js  
import { ThemeContext } from './Contexts.js' // 导入

function Button() {  
	const theme = useContext(ThemeContext) // 使用
}
```
## forwardRef
- 语法：`forwardRef(render)`
- 参数
	- `render`：组件的渲染函数
		- `props`：父组件传递来的参数
		- `ref`：父组件传递来的ref属性
- 返回值：组件
1. 暴露 DOM 节点给父组件
```jsx
// 子组件
const Input = forwardRef((props, ref) => {
	return <input ref={ref}> {/* 暴露<input>给父组件 */}
})

// 父组件
function App() {
	const ref = useRef(null)
	return <Input ref={ref} /> {/* 父组件访问<input> */}
}
```
2. 仅暴露方法给父组件，而不是整个 DOM 节点
```jsx
// 子组件
const Panel = forwardRef((props, ref) => {
	const [num, setNum] = useState(0)
	useImperativeHandle(ref, () => { // 将ref指向对象{ add }
		return {
			add() {setNum(num + 1)} // 仅暴露add()方法
		}
	})
	return <h1>{num}</h1>
})

// 父组件
function App() {
	const ref = useRef(null)
	const handleClick = () => {ref.current.add()} // 父组件只能访问add()
	return <Panel onClick={handleClick} ref={ref} />
}
```
## memo
- 效果：组件 `props` 不变时，跳过重新渲染
- 语法：`memo(component, arePropsEqual?)`
- 参数
	- `component`：组件
	- `arePropsEqual?`：一个函数，判断新旧`props`是否相同通（常不指定次函数）
- 返回值：新组件
```jsx
const Input = memo(function Input({ name }) { // 当props不变时，跳过重新渲染
	const [age, setAge] = useState(18) // 当state变化时，触发重新渲染
	const theme = useContext(ThemeContext) // 当context变化时，触发重新渲染
})
```
:::danger
- `memo` 使用Object.is判断props是否相同，因此当props为 `[]`, `{}`, `function` 时，总会重新渲染
- `memo`仅与props有关，当 `state` , `context` 改变时，会触发重新渲染
- props为 `[]`, `{}` 时使用 `useMemo` 优化，props为 `function` 时使用 `useCallback` 优化
:::
## lazy
- 效果：使组件第一次被渲染之前延迟加载
- 语法：`lazy(load)`
- 参数
	- `load`：返回 promise 的函数
- 返回值：组件，可使用 `<Suspense>` 显示一个正在加载的提示
```jsx
const Hello = lazy(() => import('./Hello.js')) // 懒加载导入

export default function Panel() {
	return (
		<Suspense fallback={<h1>loading...</h1>}> {/* 当组件正在加载时，显示<h1> */}
			<Hello /> {/* 当组件加载完成后，显示组件<Hello /> */}
		</Suspense>
	)
}
```