# 角色基础功能

**阅读本文大概需要 30 分钟**

本文概述了角色的所有基础属性，以及如何在编辑器中，修改角色的基础属性，制作出主角的各种能力。

## 什么是角色？

- 角色是具有一套行为能力的模型。游戏世界中，角色分为非玩家角色和玩家角色。
- 【玩家角色】：由玩家控制的角色，大部分的玩家角色都是游戏剧情的关键或是主角。本篇主要讲解如何设置玩家角色的属性与功能。
- 【非玩家角色】：非玩家角色也被称为 NPC，指的是在游戏中不受真人玩家控制的游戏角色。NPC 一般由计算机人工智能控制，拥有一套行为模式的角色。NPC 通常分为剧情 NPC，战斗 NPC，服务 NPC 以及兼具多种功能的 NPC 等。

## 如何设置角色属性？

### 方式一 ：面板设置

在对象管理器中的【世界】对象中，找到并点击【角色】对象后，即可通过属性面板编辑角色的默认属性。

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830170201026.png) | ![](https://arkimg.ark.online/image-20240830170331083.png) |

### 方式二 ：脚本设置

通过脚本挂在到对象管理器的对象中，实现动态修改角色属性。

下面我们会根据角色的各个属性，介绍其属性作用，以及如何动态修改角色属性。

## 角色属性

### 基础属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/be3b0b43a3af4d22817ac64bb7cf6201_360736523.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/0df4e219bd2749a28ea7cab707926f2d_360736533.webp)|

#### 是否可移动

属性说明：控制玩家移动的属性，关闭后角色将无法移动。

实际应用：我们可以利用触发器对象，制作一个小范围的陷阱。角色进入陷阱时，导致角色眩晕1秒，眩晕期间无法移动，并播放眩晕特效。当然禁锢类型的技能效果也是如此。

实现步骤：

首先我们将触发器对象放置在场景中

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830170415376.png) | ![](https://arkimg.ark.online/image-20240830170436667.png) |

然后触发器对象下挂载以下脚本，即可实现一个限制移动的陷阱。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明触发器
        let trigger = this.gameObject as Trigger;
        //角色进入陷阱触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            //角色禁止移动
            chara.movementEnabled = false;
            // 新增眩晕特效
            let effect = GameObject.spawn("142935",{replicates: true}) as Effect;
            //将眩晕特效插到角色根节点处
            chara.attachToSlot(effect, HumanoidSlotType.Root);
            //将眩晕特效进行相对位置的偏移
            effect.localTransform.position = new Vector(0, 0, 170)
            //播放眩晕特效
            effect.play();
            //加载眩晕动作
            let dizzyAnimation = chara.loadAnimation("53005");
            //播放眩晕动作
            dizzyAnimation.play();
            //1秒后触发以下逻辑
            setTimeout(() => {
                if (chara) {
                    //角色可以移动
                    chara.movementEnabled = true;
                    //移除头顶眩晕特效
                    effect.destroy()
                    //停止播放眩晕动作
                    dizzyAnimation.stop();
                }
            }, 1000);

        });

    }
}
```

效果图：

<video controls src="https://cdn.233xyx.com/online/AvLF60GkexpY1693999403176.mp4"></video>

#### 地面最大速度

属性说明：角色在地面移动时，角色可达到的最大移动速度。

实际应用：我们可以利用触发器对象，制作一个加速区域或者减速区域。当角色进入区域时，使角色获得5秒加速或者获得5秒减速。后续我们在制作跑酷类型的游戏时，可以合理利用此功能进行设计游戏玩法。

首先我们将触发器对象放置在场景中

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/9849316cfa79420aa5530d19b94e13e9_360736524.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/3bf42d1bb4d142629331c4d1129644e0_360736534.webp)|

然后触发器对象下挂载以下脚本，即可实现一个加速区域。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {

        let trigger = this.gameObject as Trigger;

        //角色进入触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            //角色的地面最大速度将变为900
            chara.maxWalkSpeed = 900;
            //加载疾跑动作
            let runAnimation = chara.loadAnimation("29722");
            //播放疾跑动作
            runAnimation.play();
            // 新增疾跑特效
            let effect = GameObject.spawn("153604",{replicates: true}) as Effect;
            //将疾跑特效插到角色根节点处
            chara.attachToSlot(effect, HumanoidSlotType.Root);
            //将疾跑特效进行相对位置的偏移
            effect.localTransform = new Transform((new Vector(0, 0, 90)),new Rotation(90, 0, -90),new Vector(1, 1, 1));
            //播放疾跑特效
            effect.play();
            //2秒后触发以下逻辑
            setTimeout(() => {
                if (chara) {
                    //角色移动模式切换为地面行走
                    chara.switchToWalking();
                    //角色的地面最大速度恢复为450
                    chara.maxWalkSpeed = 450;

                }
            }, 2000);

        });

    }
}
```


效果图：

<video controls src="https://cdn.233xyx.com/online/I3mgCfAkGrbE1693999403176.mp4"></video>

换个脚本，并将脚本中利用到的GUID为【142933】的资源放置在优先加载的列表，即可实现一个减速区域。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {

        let trigger = this.gameObject as Trigger;

        //角色进入触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            //角色的地面最大速度将变为100
            chara.maxWalkSpeed = 100;
            // 新增减速特效
            let effect = GameObject.spawn("142933",{replicates: true}) as Effect;
            //将减速特效插到角色根节点处
            chara.attachToSlot(effect, HumanoidSlotType.Root);
            //播放减速特效
            effect.play();
            //2秒后触发以下逻辑
            setTimeout(() => {
                if (chara) {
                    //角色移动模式切换为地面行走
                    chara.switchToWalking();
                    //角色的地面最大速度恢复为450
                    chara.maxWalkSpeed = 450;
                    //移除减速特效
                    effect.destroy()
                }
            }, 2000);

        });

    }
}
```

效果图：

<video controls src="https://cdn.233xyx.com/online/HYZMNa5Ojbuc1693999403176.mp4"></video>

#### 最大加速度

属性说明：角色移动时，角色会根据最大加速度进行提速，逐渐达到角色的地面最大速度。

举例说明：在两个角色的地面最大速度相同时，最大加速度较快的角色会先达到最大速度。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//设置角色最大加速度 = 1000；
chara.maxAcceleration = 1000;
```

#### 不可跨越高度

属性说明：角色跨越台阶时，台阶的最大高度 ，大于等于该高度角色均无法跨越。

举例说明：当角色上楼梯时，可以直接走上去，当角色上比较高的石墩时，无法直接走上去。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色不可跨越高度 = 200；
chara.maxStepHeight = 200;
```

示意图：

<video controls src="https://cdn.233xyx.com/online/kl30rEqnyKYi1693999403175.mp4"></video>

#### 最大站稳角度

属性说明：角色站立在斜坡上时，斜坡的最大角度，超过该角度，角色将无法站立在这个斜坡上，角色会存在坠落的表现。

数值范围：0-90。

举例说明：当角色走上斜坡时，斜坡角度过大，角色将无法在斜坡上站稳，而滑落下来。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色最大站稳角度 = 60；
chara.walkableFloorAngle = 60;
```

示意图：

<video controls src="https://cdn.233xyx.com/online/laKEaJHyAl3J1693999403176.mp4"></video>

#### 最大转向速度

属性说明：角色每秒旋转的最大速度

举例说明：当角色接到指令进行转向时，会根据转向速度进行旋转。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色最大转向速度 = 100；
chara.rotateRate = 100;
```

示意图：

<video controls src="https://cdn.233xyx.com/online/CfOiw6hqaUe11693999403176.mp4"></video>

#### 运动面朝方向

属性说明：主角模型的面部朝向。

运动面朝方向包括：始终朝向移动方向、始终朝向目标方向、始终朝向控制器方向。

举例说明：

- 始终朝向移动方向

  - 主角模型面朝方向始终朝向移动方向，例如角色向后移动，角色模型会立马转身面朝后方进行移动，注意摄像机转向不会影响角色的面朝方向。

  <video controls src="https://cdn.233xyx.com/online/i5PEMsV3BGic1693999403176.mp4"></video>

- 始终朝向目标方向

  - 主角模型面朝方向始终朝向目标方向，例如角色向后移动，但目标在前方，则角色模型会面朝前方倒退的方式进行移动。

  <video controls src="https://cdn.233xyx.com/online/A4J26RwUJWYt1693999403176.mp4"></video>

- 始终朝向控制器

  - 主角模型面朝方向始终朝向控制器，例如用户滑动了摄像机，主角模型的朝向会跟随摄像机方向进行旋转，换而言之摄像机朝向与主角模型朝向保持一致

  <video controls src="https://cdn.233xyx.com/online/tVoNip9QJ2Xl1693999403176.mp4"></video>

示例脚本：

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明角色
        let chara = Player.localPlayer.character
        //点击按键1触发以下逻辑
        InputUtil.onKeyDown(Keys.One, () => {
            //运动面朝方向为运动方向
            chara.moveFacingDirection = 0;
        });
        //点击按键2触发以下逻辑        
        InputUtil.onKeyDown(Keys.Two, () => {
            //运动面朝方向为目标方向
            chara.moveFacingDirection = 1;
        });
        //点击按键3触发以下逻辑        
        InputUtil.onKeyDown(Keys.Three, () => {
            //运动面朝方向为控制器方向
            chara.moveFacingDirection = 2;
        });

    }
}
```

#### 运动时依据的正方向

属性说明：角色模型移动时，依据的前进方向。

运动时依据的正方向包括：以定轴方向、以视线方向、以控制器方向。

举例说明：

- 以定轴方向

  - 以世界的 X 轴正方向为前进方向，例如角色向前移动，主角模型向 X 轴正方向移动了一段距离。

  <video controls src="https://cdn.233xyx.com/online/iVBYMMXvaEOv1693999403176.mp4"></video>

- 以视线方向

  - 以角色模型的视线方向为前进方向，例如角色向前移动，主角模型向他面朝的方向移动了一段距离。

  <video controls src="https://cdn.233xyx.com/online/oX15kiXEihQ81693999403176.mp4"></video>

- 以控制器方向

  - 以控制器方向为前进方向，例如角色向前移动，主角模型会跟随摄像机的方向移动了一段距离，换而言之摄像机面朝方向为正方向。

  <video controls src="https://cdn.233xyx.com/online/xondELLFqoMU1693999403176.mp4"></video>

示例脚本：

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明角色
        let chara = Player.localPlayer.character
        //点击按键1触发以下逻辑
        InputUtil.onKeyDown(Keys.One, () => {
            //以定轴方向作为移动时的正方向
            chara.movementDirection = 0;
        });
        //点击按键2触发以下逻辑        
        InputUtil.onKeyDown(Keys.Two, () => {
            //以视线方向作为移动时的正方向
            chara.movementDirection = 1;
        });
        //点击按键3触发以下逻辑        
        InputUtil.onKeyDown(Keys.Three, () => {
            //以控制器方向作为移动时的正方向
            chara.movementDirection = 2;
        });

    }
}
```

#### 启用地面摩擦力

属性说明：角色在地面移动时，会受到两种向后的因素影响，分别是地面摩擦力和行走减速度。关闭地面摩擦力的影响后，角色移动时，就只会受到行走减速度影响。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//关闭地面摩擦力
chara.groundFrictionEnabled = false;
//设置角色的行走减速度=1000
chara.brakingDecelerationWalking = 1000;
```

#### 地面摩擦力

属性说明：角色在地面移动时，会受到两种向后的因素影响，分别是地面摩擦力和行走减速度。地面摩擦力是制约人物运动时改变方向的能力效果，一般是模拟环境因素给角色带来的不同减速效果。

实际应用：角色在冰面上奔跑时，突然转向，向反方向跑，角色会因为冰面的摩擦力较小，继续向前滑行一段时间后才能停下，然后向后进行移动。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//启用地面摩擦力
chara.groundFrictionEnabled = true;
//设备角色的地面摩擦力=10
chara.groundFriction = 10;
```

#### 行走减速度

属性说明：角色在地面移动时，会受到两种向后的因素影响，分别是地面摩擦力和行走减速度。行走减速度是角色在地面移动状态下，不施加任何主动操作时受到的减速度。

实际应用：当角色不受地面摩擦力影响时，角色在减速过程中会根据角色的行走减速度进行减速，直至角色速度为0。当然正常情况下，角色是同时受到两个因素影响，所以在制作溜冰场的效果时，需要将两个值都减少到适应的值，如果其中一个值较大，都会导致角色将直接停下来。

实现步骤：以实现溜冰场的区域为例，想实现溜冰场效果目前是两种方式，首先我们要知道因为角色受到两种因素（地面摩擦力/行走减速度）的影响才能停下来，所以在做溜冰场效果时，我们需要将这两个因素的降低。

  - 方式1：两个因素值都降低，并将脚本中利用到的GUID为【151060】的资源放置在优先加载的列表，进而实现溜冰效果。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明触发器
        let trigger = this.gameObject as Trigger;
        //声明角色
        let chara = Player.localPlayer.character
        //加载溜冰动作
        let anim = chara.loadAnimation("151060");

        //角色进入触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            if (chara) {
                //降低地面摩擦力，地面摩擦力=0
                chara.groundFriction = 0;
                //降低行走减速度，使行走减速度=10
                chara.brakingDecelerationWalking = 10;
                //播放滑冰动作
                anim.play();
            }
        });
        //角色离开触发器时触发以下逻辑
        trigger.onLeave.add((chara: Character) => {
            if (chara) {
                //提高地面摩擦力，地面摩擦力=8                
                chara.groundFriction = 8;
                //提高行走减速度，使行走减速度=2048             
                chara.brakingDecelerationWalking = 2048;
                //停止滑冰动作
                anim.stop();
            }
        });

    }
}
```

  - 方式2：利用关闭地面摩擦力的接口，使地面摩擦力功能失效，只改变行走减速度，并将脚本中利用到的GUID为【151060】的资源放置在优先加载的列表，进而实现溜冰效果。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明触发器
        let trigger = this.gameObject as Trigger;
        //声明角色
        let chara = Player.localPlayer.character
        //加载溜冰动作
        let anim = chara.loadAnimation("151060");
        
        //角色进入触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            if (chara) {
                //关闭地面摩擦力的影响
                chara.groundFrictionEnabled = false;
                //降低行走减速度，使行走减速度=10
                chara.brakingDecelerationWalking = 10;
                //播放滑冰动作
                anim.play();
            }
        });
        //角色离开触发器时触发以下逻辑
        trigger.onLeave.add((chara: Character) => {
            if (chara) {
                //关闭地面摩擦力的影响
                chara.groundFrictionEnabled = false;
                //提高行走减速度，使行走减速度=2048                
                chara.brakingDecelerationWalking = 2048;
                //停止滑冰动作
                anim.stop();
            }
        });

    }
}
```

示意图：

<video controls src="https://cdn.233xyx.com/online/GqMO1Scrdw5c1693999403175.mp4"></video>

### 飞行属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/95a0f716ebe54d8da7ba847ba5b9d739_360736526.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/062e786d3a3e4cc493fd89e9f6db60fc_360736536.webp)|

#### 最大飞行速度

属性说明：角色在飞行状态下进行移动时，角色可达到的最大移动速度。

实际应用：我们可以利用触发器对象，制作一个飞行区域。当角色进入区域时，角色会切换为飞行状态，且最大飞行速度为1000。

首先我们将触发器对象放置在场景中

| 中文示例                                                     | 英文示例                                                   |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| ![img](https://qn-cdn.233leyuan.com/athena/online/9849316cfa79420aa5530d19b94e13e9_360736524.webp) | ![](https://arkimg.ark.online/image-20240830170950153.png) |

然后触发器对象下挂载以下脚本，即可实现一个飞行区域。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {

        let trigger = this.gameObject as Trigger;

        //角色进入触发器时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            if (chara) {
                //角色状态切换为飞行状态
                chara.switchToFlying();
                //角色最大飞行速度=1000
                chara.maxFlySpeed = 1000;
            }
        });

        //角色离开触发器时触发以下逻辑
        trigger.onLeave.add((chara: Character) => {
            if (chara) {
                //角色状态切换为地面行走状态
                chara.switchToWalking();
            }
        });

    }
}
```

示意图：

<video controls src="https://cdn.233xyx.com/online/P73juCE59Pxc1693999403176.mp4"></video>

#### 飞行减速度

属性说明：角色在空中移动的状态下，不施加任何主动操作时受到的减速度。

实际应用：当角色在向前飞行的时候，突然不再施加操作，角色会受到飞行减速度的影响进行减速，直至飞行速度等于0。如果飞行减速度设置较小，角色会继续向其滑行，然后慢慢停下来。如果飞行减速度设置较大，角色将直接停下来。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//飞行减速度 = 200
chara.brakingDecelerationFlying = 200;
```

飞行减速度为200时，示意图：

<video controls src="https://cdn.233xyx.com/online/0uJw20PN3L6X1693999403175.mp4"></video>

飞行减速度为2048时，示意图：

<video controls src="https://cdn.233xyx.com/online/OfbKoYSDkjle1693999403175.mp4"></video>

### 游泳属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/8ae1e6c3fe4c4fae8c494f5127a20658_360736527.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/186bbc16476c4cc0a4d512ad84164f8a_360736537.webp)|

#### 最大游泳速度

属性说明：角色在游泳状态下进行移动时，角色可达到的最大移动速度。

实际应用：当角色进入游泳区域时，角色会切换为游泳状态，且最大游泳速度为 1000。

实现步骤：首先我们将游泳区域对象放置在场景中

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/5f49306eb1e24b0dbd4f2f18e677eb09_360736528.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/45e3eb7c59be4099b420983de090b2e8_360736538.webp)|

然后我们就会根据角色属性面板中最大游泳速度，进行执行。

示意图：

<video controls src="https://cdn.233xyx.com/online/K88YYZQJSaD01693999403176.mp4"></video>

如果想动态修改最大游泳速度，示例脚本如下：

```ts
@Component
export default class NewScript extends Script {
    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明角色
        let chara = Player.localPlayer.character
        //设置最大游泳速度 = 500
        chara.maxSwimSpeed = 500;
    }
}
```

#### 游泳减速度

属性说明：角色在游泳状态下，不施加任何主动操作时受到的减速度。

实际应用：当角色在向前游泳的时候，突然不再施加操作，角色会受到游泳减速度的影响进行减速，直至游泳速度等于0。如果游泳减速度设置较小，角色会继续向其滑行，然后慢慢停下来。如果游泳减速度设置较大，角色将直接停下来。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//游泳减速度 = 500
chara.brakingDecelerationSwimming = 500;
```

游泳减速度为200时，示意图：

<video controls src="https://cdn.233xyx.com/online/DkxfmhZJwtZS1693999403176.mp4"></video>

游泳减速度为2048时，示意图：

<video controls src="https://cdn.233xyx.com/online/j0qttyJMkOvt1693999403176.mp4"></video>

### 下蹲属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/bc2454ceffcf43c3be51edc46992e549_360736529.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/8c6dce4ec64f4452bcd3e28650f08d72_360736539.webp)|

#### 是否可以下蹲

属性说明：角色是否可以下蹲。

实际应用：在特殊情况下，不允许角色下蹲。

#### 蹲伏行走最大移动速度

属性说明：角色在下蹲状态下移动时，角色可达到的最大移动速度。

实际应用：进入下蹲区域时，角色进入下蹲状态，移速在达到最大移动速度时恒定。

实现步骤：首先我们将触发器对象放置在场景中，然后绑定以下脚本即可实现一个下蹲区域。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {

        let trigger = this.gameObject as Trigger;

        //角色进入方形触发时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            if (chara) {
                //角色状态切换为下蹲状态
                chara.crouch(true);
                //设置下蹲移速=500
                chara.maxWalkSpeedCrouched = 500;
            }
        });

        //角色离开方形触发时触发以下逻辑
        trigger.onLeave.add((chara: Character) => {
            if (chara) {
                //角色状态切换为行走状态
                chara.crouch(false);
            }
        });

    }
}
```

示意图：

<video controls src="https://cdn.233xyx.com/online/haTQ8efdSOqv1693999403176.mp4"></video>

#### 下蹲时的高度

属性说明：下蹲状态下，角色胶囊体的高度。

实际应用：根据实际角色模型的下蹲高度，调整角色的胶囊体高度，进行完美适配。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色下蹲时胶囊体的高度 = 150
chara.crouchedHeight = 150;
```

### 下落属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/47dba01fb88e4659bdd9b7eecb7eef4c_360736530.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/fe5b17c2c6584221b223641f8e218511_360736540.webp)|

#### 最大下落速度

属性说明：角色在下落状态下移动时，角色可达到的最大移动速度。

实际应用：角色在从高空跳跃后，角色仅受到重力影响进行下落，下落速度会越来越快，直至移速达到最大下落速度。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//最大下落速度 = 5000
chara.maxFallingSpeed = 5000;
```

#### 下落控制灵敏度

属性说明：角色在下落状态下的运动效果，可以拆分成水平方向的运动和垂直方向的运动两个部分。垂直方向的运动计算主要通过重力影响，水平方向的运动计算与地面的运动效果是一样的，也是由地面的最大速度、最大加速度以及下落控制灵敏度进行计算。也就是说水平速度主要以地面速度为基准，而下落控制灵敏度是限制水平的移动速度效果，使角色在下落状态下的水平移动速度不超过地面的移动速度。

数值范围：0-1。

范围说明：下落控制灵敏度范围在0到1之间，如果控制程度的数值为0时，角色无法在水平方向施加其他方向的速度。如果控制程度的数值为1时，角色可以在水平方向施加其他方向的速度，并且移动速度与地面的移动速度保持一致。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//点击按键1触发以下逻辑
InputUtil.onKeyDown(Keys.One, () => {
    //下落控制程度 = 0
    chara.airControl = 0;
});
//点击按键2触发以下逻辑        
InputUtil.onKeyDown(Keys.Two, () => {
    //下落控制程度 = 1
    chara.airControl = 1;
});
```

下落控制灵敏度为0时，示意图：

<video controls src="https://cdn.233xyx.com/online/Zp5x6HDddZSv1693999403176.mp4"></video>

下落控制灵敏度为1时，示意图：

<video controls src="https://cdn.233xyx.com/online/BoepkXAN3v1I1693999403176.mp4"></video>

#### 重力倍率

属性说明：该值是调整角色下落速度时的重力效果的倍率。默认为1倍，数值越大，角色下落速度越快，数值越小，角色下落速度越慢。该值只影响角色重力倍率，世界场景中其他物体不受其影响。

实际应用：我们可以通过重力倍率调整出降落伞的效果，并且可以通过角色在空中状态下，改变地面最大速度和加速度，实现在空中移动速度更快的效果。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//重力倍率 = 0.01
chara.gravityScale = 0.01;
```

#### 下落水平减速度

属性说明：角色在下落状态下，不施加任何主动操作时水平方向受到的减速度。

举例说明：正常跳跃时，会根据初始跳跃的方向进行移动，如果该减速度过大，会导致角色在不操作的情况下，脱离该轨迹，进行垂直下落的效果。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//下落水平减速度 = 1000
chara.horizontalBrakingDecelerationFalling = 1000;
```

下落水平减速度为0时，示意图：

<video controls src="https://cdn.233xyx.com/online/JOI47FtvmJx41693999403176.mp4"></video>

下落水平减速度为1000时，示意图：

<video controls src="https://cdn.233xyx.com/online/tCewg44jAeTG1693999403176.mp4"></video>

### 跳跃属性

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/7bc23409b98b40d38833b1d7baef4da5_360736531.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/2078d2f83a7a4540a897aa095c60c263_360736541.webp)|

#### 是否可跳跃

属性说明：限制角色跳跃的属性。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色跳跃能力为false
chara.jumpEnabled = false;
```

#### 最大跳跃高度

属性说明：角色跳跃时，从起跳位置到最高位置的距离设定。

实际应用：角色踩到跳跃板（触发器）时，角色将会执行跳跃，且跳跃很高的高度。

实现步骤：首先我们将触发器对象放置在场景中，然后绑定以下脚本即可实现一个跳跃板的功能。或者也可以利用此功能做蹦蹦床等功能。

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        //声明触发器
        let trigger = this.gameObject as Trigger;

        //角色进入方形触发时触发以下逻辑
        trigger.onEnter.add((chara: Character) => {
            if (chara) {
                //角色触发跳跃功能
                chara.jump();
                //角色跳跃高度 = 800
                chara.maxJumpHeight = 800;
            }
        });

        //角色离开方形触发时触发以下逻辑
        trigger.onLeave.add((chara: Character) => {
            if (chara) {
                //角色跳跃高度 = 200
                chara.maxJumpHeight = 200;
            }
        });

    }
}
```

示意图：

<video controls src="https://cdn.233xyx.com/online/Y3RHFBVwMm8N1693999403176.mp4"></video>

#### 最大跳跃次数

属性说明：角色能够执行的最大跳跃数量。

实际应用：实现角色多次跳跃的功能。

示例脚本：

```ts
//声明角色
let chara = Player.localPlayer.character
//角色跳跃次数 = 5
chara.jumpMaxCount = 5;
```

示意图：

<video controls src="https://cdn.233xyx.com/online/Thpwn5ppWGZb1693999403176.mp4"></video>

### 形象设置

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/0b204e53bec749c98a2f2477c6687f95_360960892.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/302c75093ed2450aa612653c8a944209_360960888.webp)|

#### 使用233平台角色形象

属性说明：勾选使用平台角色形象，角色的形象会继承233的平台形象效果，不勾选的情况下，角色可以自主设置角色形象。

注意说明：勾选使用平台角色形象的情况下，在角色编辑器中修改角色形象是无法更改形象效果的。

#### 编辑人形对象

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830171058221.png) | ![](https://arkimg.ark.online/image-20240830171029486.png) |

说明：可以点击属性面板中的【编辑人形对象】进入编辑人形对象面板，为角色自定义更多装扮。

#### 体型类型

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/5fd2286ac2a740c49c65f2bd17ace4fb_360960894.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/d726e0976adf4a6b88f9773f14303daf_360960890.webp)|

属性说明：体型类型是相同外观类型下得更加详细得类型划分，使编辑器能够更加精准的获取到角色形象的体型外观，让角色的动作和表现效果更加符合审美。目前体型类型只有高级人形形象才会进行区分，【基础人形形象】和【多足形象】直接更换形象即可，不需要区分体型。

备注说明：无类型是没有明确的形象定义，让用户自定义角色效果。

#### 角色形象

属性说明：角色形象是具体的形象资源槽，根据不同的类型显示不同的资源槽，用户可以通过将资源拖拽相应属性资源槽内，快速实现设置想要的角色形象。

操作示例：

| 中文示例                                                     | 英文示例                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20240830171236840](https://arkimg.ark.online/image-20240830171236840.png) | ![image-20240830171305038](https://arkimg.ark.online/image-20240830171305038.png) |

角色换装的详细说明请见：

### 角色描边

| 中文示例    | 英文示例                                                         |
| ----------- | ------------------------------------------------------------ |
|![img](https://qn-cdn.233leyuan.com/athena/online/6b7ccaa21f5a4213aa17d33e4d45f769_360960895.webp)|![img](https://qn-cdn.233leyuan.com/athena/online/34e527a7189f49cd82eae58e6fcc293f_360960891.webp)|

#### 被遮挡时开启描边

属性说明：角色被物体遮挡时，会显示角色描边，标记角色位置。

注意说明：开启描边后，注意关闭摄像机碰撞，否则没办法看到角色在遮挡情况下的描边效果。

#### 描边颜色

属性说明：显示角色描边时，角色的描边颜色。

#### 描边宽度

属性说明：显示角色描边时，角色的描边宽度。

示意图：

<video controls src="https://cdn.233xyx.com/online/j8iCm5w8BbZ11693999403175.mp4"></video>
