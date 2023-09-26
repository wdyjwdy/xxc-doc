- 效果：保存任意值
- 值相等的判定：使用 `===` 判定（`NaN` 也视为相等）
- 值有序（插入顺序）
- 值唯一
## 基础操作
```js
const friends = new Set()

friends.add('xxc') // 增
friends.has('xxc') // true
friends.size // 1
friends.delete('xxc') // 删
friends.clear() // 清空
```
## 迭代
```js
for (const val of friends)
for (const [key, val] of friends.entries()) // key === val
for (const key of friends.keys()) // 等价于 values()
for (const val of friends.values())
friends.forEach((val, key) => {}) // key === val
```