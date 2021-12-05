## Graph2 使用文档

### 数据

- nodes: 节点数据，需要包含 id，且不不能包含特殊字符

```ts
interface IItem {
  id: string;
  name?: string;
  /** 节点填充色 */
  color?: string;
}
type Nodes = IItem[]
```

- links: 节点连接关系，需要包含 source 和 target，且不不能包含特殊字符

```ts
interface ILink {
   /** 开始节点，对应节点 ID */
  source: string;
  /** 去向节点，对应节点 ID  */
  taregt: string;
  /** 边关系 */
  type?: string;
}
type Links = ILink[]
```

### config 

- nodeStroke = '#fff',  节点边框色，默认白色
- nodeStrokeWidth = 1.5,  节点边框大小，默认 1.5
- nodeStrokeOpacity = 0.85,  节点边框透明度，默认 0.85
- nodeRadius = 32,  节点半径，默认 32 
- noLinkNodeRadius = 16, 无连接线的节点半径，默认 16
- linkStroke = '#fff',  连线颜色，默认 #fff
- linkStrokeOpacity = 0.6,  连线透明度，默认 0.6
- linkStrokeWidth = 2,  连线透宽度，默认 2
- width = 800, 画布宽度，默认 800 
- height = 800,  画布高度，默认 800 
- radius = 250,  布局半径，默认 250 
- duration = 1000,  动画持续时间， 默认 1000 ms
- showArrows = true, 是否显示尾部箭头