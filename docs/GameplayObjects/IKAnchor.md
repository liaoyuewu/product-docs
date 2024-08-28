# IK锚点

**阅读本文大概需要 10 分钟**

本文概述了IK的定义，IK锚点都有哪些功能，表现效果以及使用方式。

## 什么是IK？

功能说明：反向运动（Inverse Kinematics，简称IK）是一种在计算机动画中使用骨骼系统来模拟物体的动作的技术。这种技术可以让物体的部分（如手臂、腿等）随着根部的移动而自动调整位置，从而实现更加自然和逼真的动画效果。
我们提供了【自动IK】功能，方便角色脚部自适应各种地面效果。包括不平整的地面、楼梯等。

![image-20240828203008689](https://arkimg.ark.online/image-20240828203008689.png)

除了【自动IK】功能，我们还提供了【IK锚点】的逻辑对象，方便用户在使用涉及到手部或脚部的交互对象时，能够控制角色的手部位置或脚部位置，防止穿模现象。举例说明，比较典型的手部交互对象就是武器（弓箭/热武器等），脚部交互对象就是载具坐骑（车/马等）

| 中文示例      | 英文示例 |
| ----------- | ----------- |
| ![image-20240828203629104](https://arkimg.ark.online/image-20240828203629104.png) | ![image-20240828203847352](https://arkimg.ark.online/image-20240828203847352.png) |




## 启用自动IK

功能说明：角色脚部位置会向下打射线检测地面距离，并启用IK效果，尽量让角色脚贴合地面，减少游戏中角色站在不规则地面上时的穿模现象，提升游戏表现效果。

演示效果：
![image-20240828202908552](https://arkimg.ark.online/image-20240828202908552.png)
![image-20240828203008689](https://arkimg.ark.online/image-20240828203008689.png)

相关接口：

```ts
//当前客户端玩家角色启用自动IK
IKAnchor.autoEnableIK(Player.localPlayer.character, true);
//当前客户端玩家角色关闭自动IK
IKAnchor.autoEnableIK(Player.localPlayer.character, false);
```

## IK锚点的使用

### 1、如何找到IK锚点？

在【资源库】-【游戏功能对象】列表中找到【IK锚点】的对象。

| 中文示例      | 英文示例 |
| ----------- | ----------- |
| ![image-20240828203629104](https://arkimg.ark.online/image-20240828203629104.png) | ![image-20240828203847352](https://arkimg.ark.online/image-20240828203847352.png) |

### 2、使用IK锚点

以射击游戏中角色举枪瞄准为例，如果我们在不使用IK锚点的情况下，左手穿模情况效果如下：

![](https://arkimg.ark.online/image-20240828213844068.png)


找到IK锚点对象后，可以手动拖入到步枪模型对象下。首先将Anchor Type属性改为“左手”，该属性决定IK效果作用到的角色骨骼。调整IK锚点的世界坐标，尽量贴合我们想要让角色左手想要放置的位置。新建脚本NewScript挂载至步枪模型对象下，并在onStart方法中粘贴代码完成以下逻辑：进入游戏后按下键盘按键“1”拾取步枪后，播放持枪瞄准姿态并启用IKAnchor；

```ts

@Component
export default class NewScript extends Script {

    model: Model;
    ik: IKAnchor;


    protected onStart(): void {

        this.model = this.gameObject as Model;
        this.ik = this.model.getChildByName("IKAnchor") as IKAnchor;


        InputUtil.onKeyDown(Keys.One, () => {

            this.attachToSlot(Player.localPlayer.character);
            
        });
    }

    @RemoteFunction(Server)
    private attachToSlot(character: Character) {
        
        character.attachToSlot(this.model, HumanoidSlotType.RightHand);
        this.model.localTransform.position = new Vector(3, -2, 0);
        this.model.localTransform.rotation = Rotation.zero;
        character.loadSubStance("49098").play();
        this.ik.active(character);

    }
}
```
| 中文示例      | 英文示例 |
| ----------- | ----------- |
| ![](https://arkimg.ark.online/image-20240828211748765.png) | ![](https://arkimg.ark.online/image-20240828211710641.png) |

运行项目通过按键触发我们的角色IK功能后，将会看到角色会按照我们的IK锚点放置左手位置。

![](https://arkimg.ark.online/image-20240828213607846.png)

我们在触发了脚本后，手部效果明显好了一些。

![](https://arkimg.ark.online/image-20240828213700848.png)


### 3、关闭IK锚点

启用IK锚点后，角色无论是播放动画，还是做其他的事情，都会根据IK锚点进行移动，举例说明，比如我们已经卸载武器了，本质上就不需要将手部IK的功能了，这个时候，就需要将IK功能关闭掉，防止影响我们角色的移动等其他的交互功能。所以在脚本中，添加关闭IK锚点的代码：按下键盘按键“2”卸载步枪并恢复默认姿态：

```ts

@Component
export default class NewScript extends Script {

    model: Model;
    ik: IKAnchor;
	trans: Transform;

    protected onStart(): void {

        this.model = this.gameObject as Model;
     	this.trans = this.model.worldTransform.clone();
        this.ik = this.model.getChildByName("IKAnchor") as IKAnchor;


        InputUtil.onKeyDown(Keys.One, () => {

            this.attachToSlot(Player.localPlayer.character);
            
        });
    }

    @RemoteFunction(Server)
    private attachToSlot(character: Character) {
        
        character.attachToSlot(this.model, HumanoidSlotType.RightHand);
        this.model.localTransform.position = new Vector(3, -2, 0);
        this.model.localTransform.rotation = Rotation.zero;
        character.loadSubStance("49098").play();
        this.ik.active(character);

    }
    
    @RemoteFunction(Server)
    private detachFromSlot(character: Character) {
        
        character.detachFromSlot(this.model);
        this.model.worldTransform = this.trans;
        character.currentSubStance.stop();
        this.ik.deactivate();

    }
}
```


### 4、动态生成IK锚点

我们可以通过将鼠标悬停在逻辑对象的上方，查看资源ID；也可以通过右键，复制对象的资源ID。然后通过资源ID动态创建相应的资源。

| 中文示例      | 英文示例 |
| ----------- | ----------- |
| ![image-20240828203629104](https://arkimg.ark.online/image-20240828203629104.png) | ![image-20240828203847352](https://arkimg.ark.online/image-20240828203847352.png) |

动态生成IK锚点的示例脚本：

```ts
// 异步spawn，没有找到资源时会下载后在生成
GameObject.asyncSpawn("IKAnchor").then((obj) => {
    let IKObject = obj as IKAnchor;
})
```

```ts
// 普通spawn生成，没有优先加载或预加载资源则无法生成
let IKObject = GameObject.spawn("IKAnchor") as IKAnchor;
```

## IK锚点属性介绍

### 1、IK类型

功能说明：IK类型主要是决定IK锚点影响角色的骨骼位置，目前我们提供的骨骼位置只有4个，分别是左手，右手，左脚，右脚。

演示效果：
| 右手    | 左手 | 右脚 |左脚|
| ----------- | ----------- |----------- | ----------- |
| ![](https://arkimg.ark.online/image-20240828205612265.png) | ![](https://arkimg.ark.online/image-20240828205802672.png) | ![image-20240828205852685](https://arkimg.ark.online/image-20240828205852685.png) | ![image-20240828205828988](https://arkimg.ark.online/image-20240828205828988.png) |

### 2、关节朝向

功能说明：修改关节朝向，会影响到IK类型所关联的骨骼空间朝向，比如说手部IK的相连骨骼就是肘部的关节，脚部IK的相连骨骼就是膝盖关节，关节朝向会直接影响到肘部关节和膝盖关节的朝向问题。

演示效果：
| 关节朝向X | 关节朝向Y | 关节朝向Z |
| ----------- | ----------- |----------- |
| ![](https://arkimg.ark.online/image-20240828210156238.png) | ![](https://arkimg.ark.online/image-20240828210228352.png) | ![](https://arkimg.ark.online/image-20240828210257748.png) |
| ![](https://arkimg.ark.online/image-20240828210207435.png) | ![](https://arkimg.ark.online/image-20240828210241014.png) | ![](https://arkimg.ark.online/image-20240828210310654.png) |


相关接口：

```ts
//通过GUID找到对应的IK锚点对象
let IK = GameObject.findGameObjectById("3BAABEBE") as IKAnchor;
//将IK应用到当前角色上
IK.active(Player.localPlayer.character); 
// 设置IK的关节锚点为（90,0,0）
IK.jointTarget = new Vector(90,0,0)
```

### 3、权重

功能说明：在激活IK锚点功能时，角色动画和IK效果的混合权重，取值范围在0-1之间，值为0时，则全依据角色动画效果，值为1时，则全依据角色IK效果。

演示效果：

| weight = 0 | weight = 0.5 | weight = 1 |
| ----------- | ----------- |----------- |
| ![](https://arkimg.ark.online/image-20240828210454171.png) | ![](https://arkimg.ark.online/image-20240828210520777.png) | ![](https://arkimg.ark.online/image-20240828210533185.png) |


相关接口：

```ts
// 将IK权重设置为1
IK.weight = 1
```

### 4、混入/混出时间

功能说明：激活IK锚点后，当前动画混合到IK效果时需要的时间；停用IK锚点后，从IK混合效果恢复到角色正常动画需要的时间。

演示效果：

<video controls src="https://arkimg.ark.online/20240828-215941.mp4"></video>

相关接口：

```ts
IK.blendInTime = 5;
IK.blendOutTime = 0;

```

