- 效果：保存键值对，键可以为任意值
- 键相等的判定：使用 `===` 判定（`NaN` 也视为相等）
- 键唯一
- 键有序（插入顺序）
## 基础操作
```js
const friends = new Map()

friends.set('xxc', { age: 18}) // 增
friends.get('xxc') // 查
friends.has('xxc') // true
friends.size // 1
friends.delete('xxc') // 删
friends.clear() // 清空
```
## 迭代
```js
for (const [key, val] of friends)
for (const [key, val] of friends.entries())
for (const key of friends.keys())
for (const val of friends.values())
friends.forEach((val, key) => {})
```
## 构造
```js
let arr = [
	['a': 1],
	['b': 2],
]
let map = new Map(arr)
```
## Map 和 Object 的区别
|  | `Map` | `Object` |
| --- | --- | --- |
| 键的值 | 任意 | `string` / `symbol` |
| 键是否有序 | 有（插入顺序） | 无 |
| 获取键的个数 | size() | 无 |
| 是否可迭代 | 是 | 否 |
| 序列化 | 不支持 | 支持 |