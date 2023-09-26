![flex](/img/doc/flex.png)
- 使用flex布局：将容器的 `display` 属性设置为 `flex` 或 `inline-flex`
## Flex Container
- `flex-direction`: main axis的方向
- `flex-wrap`: item是否换行
- `justify-content`: item在main axis上的对齐方式
- `align-items`: item在cross axis上的对齐方式
- `align-content`: main axis在cross axis上的对齐方式（当item可换行时，会产生多根axis）
## Flex Item
- `order`: item的排列顺序
- `flex`: grow, shrink, basis的简写
	- `flex-grow`: 延展item，可按比例分配空间
	- `flex-shrink`: 收缩item（当item总长度溢出时生效）
	- `flex-basis`: 延展收缩前item的初始大小
- `align-self`: 设置单个item的对齐方式