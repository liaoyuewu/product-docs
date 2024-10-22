# 寻路链接

::: info
阅读本文预计 10 分钟
本文概述了如何在编辑器中,使用寻路链接将两个分离的寻路区域进行链接。
:::

## 1. 什么是寻路链接

寻路链接是寻路系统中的一个逻辑对象，其功能是将两个分离的寻路区域进行链接，使代理可以跨区域进行寻路导航。

## 2. 如何使用寻路链接

### step.1
在编辑器左侧【游戏功能对象】选项中,找到【寻路链接】,拖拽到主视口,即可完成创建。

| 中文示例 | 英文示例 |
| - | - |
| ![](https://qn-cdn.233leyuan.com/online/OP4GcewOipwH1725005569635.png) | ![](https://qn-cdn.233leyuan.com/online/dH4B7y2xroic1725005679279.png) |

### step.2
在视口中选中寻路链接的接入点，移动位置，将接入点放置在需要链接区域的位置上。

| 中文示例 | 英文示例 |
| - | - |
| <video controls src="https://qn-cdn.233leyuan.com/online/vAyCJEsbBp7S1728623626305.mp4"></video> | <video controls src="https://qn-cdn.233leyuan.com/online/xiCgHUG5ob8Z1728623628009.mp4"></video> |

::: warning Precautions
寻路链接只能在相同或相邻的tile中进行链接，跨tile时链接将会失效。
tile可以在世界设置中打开/关闭显示。
:::

### step.3
设置寻路链接属性：

- 左点相对位置：左侧的链接点位置
- 右点相对位置：右侧的链接点位置
- 连接方向：该链接是双向的还是单向的
- 连接区域类型：如果设置为**高优先级**则比起普通寻路会先通过Navlink，反之则会选择其他高优先级的Navlink或者普通寻路路径，若设置为**无效**则永远不会选择该路径。

### step.4
设置完成后，寻路链接的效果立即生效，可以通过Navigation.navigateTo()查看实际效果。
