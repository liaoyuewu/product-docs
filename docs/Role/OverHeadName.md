# 头顶名称

**阅读本文大概需要 5 分钟**

本文概述了头顶名称的定义，以及如何修改头顶名称。

## 头顶名称介绍

![img](https://qn-cdn.233leyuan.com/athena/online/fdda245e49fc4d049ca106b26ff48ccc_361029366.webp)   

头顶定义：角色模型头顶会显示名称等效果。

## 如何修改头顶名称？

### 修改头顶名称

功能说明：我们可以获取并修改头顶名称

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
// 角色名称修改为哈哈哈
chara.displayName = "哈哈哈";
```

效果图：

<video controls src="https://cdn.233xyx.com/online/GeygvnZQT7qd1694767738691.mp4"></video>

### 头顶名称显示/隐藏

功能说明：我们可以通知所有角色的头顶名称的显隐。

示例脚本：

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //设置当前客户端所有角色的头顶名称不可见
        Character.nameVisible = false

    }
}
```

效果图：

<video controls src="https://cdn.233xyx.com/online/1n26Jlypp9Ku1694767738691.mp4"></video>

### 头顶UI的替换

功能说明：使用UI功能替换掉头顶UI名称。

实际应用：

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830164908507.png) | ![](https://arkimg.ark.online/image-20240830165018341.png) |

首先我们点击新建UI的按钮，创建一个新的UI文件

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830165036661.png) | ![](https://arkimg.ark.online/image-20240830165119052.png) |

然后双击点开UI文件，编辑UI内容，拖入图片组件，然后将中意的图片替换到图片组件的属性上，并点击保存，这样我们就完成了UI的编辑效果。

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830165604943.png) | ![](https://arkimg.ark.online/image-20240830165631485.png) |

退出UI编辑器，回到主编辑器后，右键UI文件，选择复制UI的工程内容ID。

最后编写以下脚本，将工程内容ID进行替换，即可实现上述效果。

```ts
@Component
export default class NewScript extends Script {
    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {
        //声明角色
        let chara = Player.localPlayer.character
        //当用户按住了'1'按键时，键触发以下逻辑
        InputUtil.onKeyDown(Keys.One, () => {
            //获取角色头顶UI，并重新绑定角色头顶UI文件的ID
            chara.overheadUI.setUIbyGUID("E5A155854B66F7D69026B9B266688AAA");
        })
    }
}
```

效果图：

<video controls src="https://cdn.233xyx.com/online/nRNkxDdtE1RN1694767738691.mp4"></video>
