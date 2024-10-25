# UI 控件-列表视图/瓦片视图/树视图

**阅读本文大概需要 5 分钟**

本文概述了 UI 控件—列表视图/瓦片视图/树视图的使用方法。

## 什么是列表视图/瓦片视图/树视图？
- 列表视图是可以显示大量条目的虚拟列表，相比于简单的使用滚动框结合容器布局功能来容纳多行条目，列表视图内仅会创建可见的条目以提升性能。例如一个只有5个条目可见的列表视图中容纳了50个项目（Item），并不会真的创建50个项目的UI，而只会创建当前实际可见的5个项目的UI。推荐列表中要展示较多项目时使用列表视图控件，而不是使用滚动框和容器布局功能。
  - 以及容器自动布局比较难维护每个条目的顺序信息，如果维护交换条目位置、把新条目插入指定位置的逻辑，推荐使用列表视图/瓦片视图/树视图。
- 瓦片视图与列表视图相似，区别仅是条目以瓦片集排列。
- 树视图同样类似于列表视图，区别在于树视图支持分层列表，其中具有包含嵌套项的展开/折叠项目，可用于编辑器插件中说明文件夹结构或对象父子级关系。
- 变换/对齐/通用/渲染属性请见 [UI 控件的基础属性](https://docs.ark.online/UI/UIWidget-BaseProperties.html)

## 列表视图/瓦片视图/树视图属性-样式
- 大部分对于列表视图/瓦片视图/树视图中项目的操作都需要在脚本中控制，属性面板上只能调整列表视图/瓦片视图/树视图的一些基础样式。

### 条目UI文件
- 设置此列表视图/瓦片视图/树视图的各项目所绑定的默认UI文件，在脚本中可以通过修改列表视图节点数据基类（即ListViewItemDataBase）针对每行项目的UI做单独的调整。
- 请注意条目UI文件只能在属性面板上或者newObject动态创建时设置，不能在创建好列表视图/瓦片视图/树视图后脚本动态设置。

### 滚动朝向
- 设置列表视图/瓦片视图/树视图中滚动条的滚动朝向，这决定了各项目UI是水平还是垂直方向排列的。
### 条目边距
- 用于调整各项目UI之间的间距。
### 菜单样式
#### 菜单行激活图片/悬浮图片/普通图片
- 设置菜单行在选中/悬浮/普通这三种状态下的样式，这些图片会显示在项目UI的下方，作为默认的项目底板图。
### 滚动条样式
#### 滚动条宽度/滚动条图片
- 设置滚动条的宽度以及图片，与滚动框的用法相同
#### 滚动条边距
- 菜单范围靠右侧的滚动条宽度的范围内为滚动条初始位置，滚动条边距可以基于这个初始位置让滚动条显示在一个有偏移的位置



## 如何使用列表视图/瓦片视图/树视图？
### 使用列表视图
- 下面我们实现一个基础的列表视图，每行按顺序显示不同的数字。
- 第一步：新建一个UI文件，这个UI文件内存放各项目UI的默认UI样式，**请注意UI文件RootCanvas层级的大小和对齐方式，共同决定了该项目在列表视图中占据的空间**（垂直滚动的列表视图中，项目UI文件垂直方向需要设置为向上对齐；水平滚动的列表视图中，项目UI文件水平方向需要设置为向左对齐）。
![](https://cdn.233xyx.com/online/t506mfXE9wHx1718169533166.png)
- 第二步：创建一个列表视图，将上一步创建的条目UI文件拖入到属性面板，并修改列表视图的基础样式，包括滚动朝向、条目边距、菜单行样式、滚动条样式。
![](https://cdn.233xyx.com/online/4rpEYGeYZDg31718169532608.png)
- 第三步：先编写条目UI文件对应的脚本，获取UI文件中的文本框，这里我们将列表视图实现节点数据基类（ListViewItemDataBase）的序号填入文本框，实际项目中这里可以写更多逻辑，让每行项目展示不同的内容。

```ts
// 继承实现ListViewItemDataBase，由于 baseGuid 是唯一值，不可控，因此新增一个自定义数据即可，本例中该值为 itemIndex
export default class ListViewItemData extends ListViewItemDataBase {
    itemIndex : number = 0
    
    constructor(index: number) {
        super()
        this.itemIndex = index
    }
}
```
```ts
export default class NewUIScript1 extends UIScript {
  TextBlock : mw.TextBlock;

    protected onStart() {
        this.TextBlock = this.uiWidgetBase.findChildByPath('RootCanvas/TextBlock') as TextBlock;
    }

    set data(inRowData : ListViewItemData) {
        // 使用继承类的 itemIndex 作为固定索引值
        this.TextBlock.text = inRowData.itemIndex.toString();
    }
}
```
- 第四步：编写列表视图所在UI文件对应的脚本，这里我们写一个按下数字键1新增项目的逻辑便于测试效果：
  - 触发列表视图刷新onItemRefreshed时，通过实现节点数据基类（ListViewItemDataBase）刷新各UI项目的表现；增加/删除/修改/玩家滚动列表视图/请求刷新等操作都会触发刷新，而清空不会触发刷新。
 
```ts
export default class NewUIScript extends UIScript {

    /** 
     * 构造UI文件成功后，在合适的时机最先初始化一次 
     */
    protected onStart() {
        this.ListView = this.uiWidgetBase.findChildByPath('RootCanvas/ListView') as mw.ListView;
        let index = 0
        //按下数字键1新增项目
        InputUtil.onKeyDown(Keys.One, () => {
            // 代码动态添加一份 ListViewItemData 数据进入 ListView
            this.ListView.addItems([new ListViewItemData(index)]);
            index++
        });

        this.ListView.onItemRefreshed.add((newWorkList : ListViewItemData[])=>{
          console.log("_____onItemRefreshed");
          newWorkList.forEach((workItem : ListViewItemData)=>{
            const typeSc = mw.findUIScript(workItem.widgetCanvas) as any;
            typeSc.data = workItem;
          });
        });
    }
}
```

::: tip
请注意如果是瓦片视图，需要在上述代码获得TileView控件之后，需要手动设置排列规则如下
:::
```ts
this.TileView = this.uiWidgetBase.findChildByPath('RootCanvas/TileView') as mw.TileView
// 通过设置子对象的宽高来决定对象的排列规则
// 本例中TileView宽400+，这里设定itemWidth200
// 因此如下图一排容纳了两个
this.TileView.itemWidth = 200
this.TileView.itemHeight = 50

// TileView继承自ListView，相关数据也保持一致
let arrTileV:ListViewItemData[] = []
for(let index = 0; index < 10; index++){
    arrTileV.push(new ListViewItemData(index))
}
tileV.addItems(arrTileV)
```


- 启动游戏后，我们按下数字键1，就能在列表视图中动态生成新项目了，并且每个项目中的数字都是节点数据基类的序号。
![](https://cdn.233xyx.com/online/69BYcSYeLEIR1718169531421.gif)

### 使用瓦片视图

- 使用瓦片视图时，需要在上述代码获得TileView控件之后，还需要手动设置排列规则如下：

```ts
this.TileView = this.uiWidgetBase.findChildByPath('RootCanvas/TileView') as mw.TileView
// 通过设置子对象的宽高来决定对象的排列规则
// 本例中TileView宽400+，这里设定itemWidth200，一排能容纳两个项目
this.TileView.itemWidth = 200
this.TileView.itemHeight = 50

// TileView继承自ListView，相关数据也保持一致
let arrTileV:ListViewItemData[] = []
for(let index = 0; index < 10; index++){
    arrTileV.push(new ListViewItemData(index))
}
tileV.addItems(arrTileV)
```


### 使用树视图
- 相比列表视图和瓦片视图使用的ListViewItemDataBase，树视图中TreeViewItemDataBase中可以记录父子级的信息
- 请注意点击包含嵌套项的项目是否会展开/折叠的逻辑需要自行编写
```ts
    this.TreeView.onItemRefreshed.add((newWorkList : mw.TreeViewItemDataBase[])=>{
        console.log("_____onItemRefreshed");
      newWorkList.forEach((workItem : mw.TreeViewItemDataBase)=>{
        const typeSc = mw.findUIScript(workItem.widgetCanvas) as any;
        typeSc.data = workItem;
      });
    });
    
    //控制项目后展开/折叠
    this.TreeView.onItemClicked.add((clickedItem: TreeViewItemDataBase, doubleClick: boolean)=>{
        let expansion = this.TreeView.getItemExpansion(clickedItem);
        this.TreeView.toggleItemExpansion(clickedItem);

    })
    //设置子级缩进距离,并刷新
    InputUtil.onKeyDown(Keys.One, () => {
        this.TreeView.itemIndentAmount=100
        this.TreeView.resetListItems(this.TreeView.listItems);

    });
    //修改第0条的展开状态    
    InputUtil.onKeyDown(Keys.Two, () => {
        this.TreeView.toggleItemExpansion(this.TreeView.listItems[0])
        console.log(this.TreeView.getItemExpansion(this.TreeView.listItems[0]));
    });
    //将第0条展开
    InputUtil.onKeyDown(Keys.Three, () => {
        this.TreeView.setItemExpansion(this.TreeView.listItems[0],true)
        console.log(this.TreeView.getItemExpansion(this.TreeView.listItems[0]));
    });
    //选中当前所有展开的条目
    InputUtil.onKeyDown(Keys.Four, () => {
        this.TreeView.setSelectionItem(this.TreeView.getExpandedItems(),true)
    });
    //可以先点击按钮添加4行然后赋予父子级关系
    InputUtil.onKeyDown(Keys.Five, () => {
        console.log(this.TreeView.listItems[3].parent);
        this.TreeView.listItems[1].parent=this.TreeView.listItems[0]
        this.TreeView.listItems[1].parent=this.TreeView.listItems[0]
        this.TreeView.listItems[1].parent=this.TreeView.listItems[0]
        this.TreeView.resetListItems(this.TreeView.listItems);
        console.log(this.TreeView.listItems[3].parent);
        
    });
    //也可以先创建并赋予父子级关系之后再添加
    InputUtil.onKeyDown(Keys.Six, () => {
        let newparent= new TreeViewItemDataBase
        let child1=new TreeViewItemDataBase
        let child2=new TreeViewItemDataBase
        let child3=new TreeViewItemDataBase
        newparent.children.push(child1);
        newparent.children.push(child2);
        newparent.children.push(child3);
        this.TreeView.addItems([newparent]);
    });

    this.TreeView.onItemExpansionChanged.add((targetItem: TreeViewItemDataBase, bExpanded: boolean)=>{
        console.log("_____onItemExpansionChanged"+targetItem+bExpanded);
    });

    this.Button.onClicked.add(()=>{
      this.TreeView.addItems([new mw.TreeViewItemDataBase]);
    });

```
- 树视图在游戏运行中的表现如下：
![](https://qn-cdn.233leyuan.com/online/4n77adYZpokT1723786744210.gif)
