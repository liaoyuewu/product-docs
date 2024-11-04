# 摄像机

**阅读本文大概需要 20 分钟。**

本文概述了如何通过修改摄像机的各种属性，实现各种各样的镜头效果。

## 什么是摄像机？
::: tip
摄像机就像我们的眼睛， 决定了玩家在3D游戏世界中看到什么样的画面。
:::

- 新建项目的【对象管理器-世界对象】中会有一个自带的无法被删除的摄像机对象；游戏开始时，会默认展示这个摄像机所看到的画面；
- 也可以从【资源库-游戏功能对象】中拖出或者在脚本中动态创建任意个摄像机对象，这些摄像机对象可以被放置在场景中的任意位置，或者挂载到其他对象下作为子级对象；
- 世界对象-摄像机与游戏功能对象-摄像机都可以通过脚本中Camera类的属性/接口来调整效果。

![](https://cdn.233xyx.com/online/XnLugKTQJ9Ha1695279533730.png)

## 摄像机属性说明

### 2.1 摄像机设置

#### 相对位置和相对旋转
- 即摄像机相对于弹簧臂的偏移和旋转，下文会具体介绍弹簧臂的定义，目前我们这里可以把主视口中的红色细线理解为弹簧臂
- 建议仅在摄像机朝向模式=固定/跟随朝向时调整摄像机相对旋转，如果当摄像机朝向模式=控制朝向时，务必将相对旋转清零，否则玩家会无法合理的操纵摄像机方向。
- 也请谨慎调整摄像机相对位置，当相对位置不在原点位置时，可能会导致弹簧臂触发摄像机碰撞后，摄像机的位置无法跟随弹簧臂伸缩至障碍物的同一侧；例如，如果希望让摄像机离角色远一些，建议调整弹簧臂长度，而不是调整摄像机相对位置
- 请注意区分摄像机相对位置/旋转与弹簧臂相对位置/旋转两者之间的区别，下文会具体介绍弹簧臂相对位置/旋转的作用。
![](https://cdn.233xyx.com/online/P1OKDAh0guwC1695279533730.gif)
![](https://cdn.233xyx.com/online/yLAvE1uxaRqV1695279533730.gif)

#### 摄像机预设
- 为了开发者能快捷选择需要的视角，编辑器提供了若干套预置的【摄像机模式】，开发者可以在属性面板或脚本中的currentCameraMode属性快捷选择想要使用的摄像机模式，而不需要手动调整众多参数；
- 使用某个摄像机模式后，各相关的摄像机属性会按照这一预置模式的预设值自动刷新。
::: tip
由于摄像机的各项参数较为复杂，强烈推荐新入门开发者使用这些预置摄像机模式；或者在这些预置摄像机模式的基础上微调。
:::

| **枚举名称** | **英文名称**   | **枚举值** | **说明**               |
| -------------------- | ---------------------- | ------------------ | ------------------------------ |
| 第一人称           | FirstPerson          | 0                | 第一人称视角的摄像机效果     |
| 第三人称           | ThirdPerson          | 1                | 第三人称视角的摄像机效果     |
| 俯视角             | TopDownAngle         | 2                | 俯视角45度的摄像机效果       |
| 默认               | Default              | 3                | 类似樱花校园模拟器的默认效果 |
| TPS过肩视角        | TPSOverShoulderAngle | 4                | 第三人称过肩视角的摄像机效果 |
| FPS射击视角        | FPSShootingAngle     | 5                | 第一人称射击视角的摄像机效果 |

* 推荐在射击类游戏中使用TPS过肩视角和FPS射击视角
  * FPS射击视角下，摄像机只能能看见玩家角色手持的武器，而看不到玩家角色
  * TPS过肩视角下，摄像机能看见玩家的上半身，且玩家在屏幕的左下区域，不会遮挡正前方视野

![](https://cdn.233xyx.com/online/h62hUrTlvIme1711267443832.gif)

#### 视场
- 视场（FOV），也就是透视模式下的水平视野角度，FOV越大，可见的视野角度越大。

| **视场为60时** | ![](https://cdn.233xyx.com/1684475954533_734.png) |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **视场为90时**  | ![](https://cdn.233xyx.com/1684475954609_668.png) |
| **视场为120时** | ![](https://cdn.233xyx.com/1684475954510_291.png) |

::: tip
如果需要使用正交视角（例如制作拥有3D效果的2D休闲游戏），推荐使用 **视场=35（或更小）** ，并同时调整**摄像机距离、位置、角度**等参数来模拟正交视角
:::

![](https://cdn.233xyx.com/1684475955081_332.png)

#### 摄像机位置模式
- 固定模式
  - 摄像机固定在某一位置，不可移动。
- 跟随模式
  - 摄像机跟随某个物体（默认是玩家角色）一直移动。

#### 摄像机朝向模式/使用控制器控制摄像机旋转
- 固定朝向：摄像机固定朝向某一个方向。
- 跟随朝向：摄像机跟随目标面朝方向。
- 控制朝向：由UI控件-摄像机滑动区控制此摄像机弹簧臂相对旋转，动态设置的弹簧臂相对旋转不会生效。
- 使用控制器控制摄像机旋转：开启时，玩家能使用摄像机滑动区控制摄像机旋转（一般情况不需要手动设置这个开关，推荐设置朝向模式）。如果您调整了摄像机朝向模式，编辑器会自动调整是否使用控制器控制摄像机旋转：使用摄像机朝向模式=固定朝向/跟随朝向时，会自动开启使用控制器控制摄像机旋转；使用摄像机朝向模式=控制朝向，会自动关闭使用控制器控制摄像机旋转。
- 请注意：当开启使用控制器控制摄像机旋转（或者摄像机朝向模式=控制朝向）时，弹簧臂的方向需要用控制器旋转（Player.getControllerRotation/Player.setControllerRotation）来获取或者设置；当关闭使用控制器控制摄像机旋转（或者摄像机朝向模式=固定朝向/跟随朝向）时，弹簧臂的方向由弹簧臂相对旋转（springArm.localTransform.rotation）来获取或者设置。

#### 固定摄像机高度
- 固定摄像机在Z轴上的坐标，比如角色在跳跃或者上楼梯时，摄像机不会跟随角色改变高度，用于制作俯视角游戏。


### 2.2 弹簧臂设置
- 首先，简单说明一下摄像机的弹簧臂有什么作用？
  - 弹簧臂能为摄像机提供根据场景情况进行扩展或回缩的功能，例如遇到障碍物时自动移动摄像机的位置，防止视线遮挡。
  - 弹簧臂的方向可以由UI控件-摄像机滑动区或者摇杆被玩家控制，便于在游戏中实现视角转动效果。
  - 目前我们这里可以把主视口中的红色细线理解为弹簧臂

#### 相对位置和相对旋转
- 相对位置，即弹簧臂挂点相对父节点的位置，父节点一般为玩家角色，摄像机通过弹簧臂挂点连接到角色。
  - 弹簧臂挂点的位置决定了玩家转动视角时，镜头围绕哪个点旋转，大多数第三人称游戏会把弹簧臂挂点放在角色的头部或者脖子，以保证角色的上半身始终位于屏幕正中央。
- 相对旋转，即弹簧臂相对于默认方向的角度，在游戏中拖动屏幕转动视角就相当于动态调整此属性，该属性可用于制作固定的俯视角或斜45°视角。请注意：
  - 当摄像机朝向模式=控制朝向（即使用控制器控制摄像机旋转开启时），由UI控件-摄像机滑动区控制此摄像机弹簧臂相对旋转，动态设置的弹簧臂相对旋转不会生效
  - 当摄像机朝向模式=固定朝向/跟随朝向（即使用控制器控制摄像机旋转不开启时），动态设置的弹簧臂相对旋转才会生效
    ![](https://cdn.233xyx.com/online/PL6bqcLnC4Mt1695279533730.gif)
    ![](https://cdn.233xyx.com/online/FZJgkk19xjaw1695279533730.gif)
::: tip
注意区分：摄像机的相对位置/相对旋转vs弹簧臂的相对位置/相对旋转
- 摄像机的相对位置/相对旋转仅改变摄像机，不影响弹簧臂；而调整弹簧臂的相对位置/相对旋转时，会连带摄像机的位置和方向一起变化。
  - 可以把弹簧臂理解成自拍杆，自拍杆的近端由自拍者手持，远端固定相机；摄像机的相对位置/相对旋转仅调整远端相机的位置和方向，不会影响自拍杆；而弹簧臂的相对位置/相对旋转调整自拍杆（会连带相机）的位置和方向。
:::
- 如果想要动态设置弹簧臂的方向，例如重置摄像机到角色正背后，但而后仍希望弹簧臂继续由玩家控制方向，可以使用如下两种方法：
```TypeScript
    //方法一：先关闭使用控制器控制摄像机旋转，然后将弹簧臂相对旋转设置为(0,0,0)
    attackBtn.onPressed.add(()=>{
        Camera.currentCamera.springArm.useControllerRotation = false;
        Camera.currentCamera.springArm.localTransform.rotation=new Rotation(0,0,0);
        Camera.currentCamera.springArm.useControllerRotation = true;
    })
    //方法二：如果此时是开启使用控制器控制摄像机旋转的状态，可以直接将覆写控制器旋转的值，也能实现相同的效果
    attackBtn.onPressed.add(()=>{
        Player.setControllerRotation(new Rotation(0,0,0));
    })  
```
- 效果示例
![](https://cdn.233xyx.com/online/ghiaeSrCT73p1695279533730.gif)

#### 弹簧臂长度
- 可以把场景中这条红色细线视作摄像机弹簧臂，修改弹簧臂长度的同时也会修改红色细线长度。
  - 摄像机到角色之间的实际距离由摄像机相对位置、弹簧臂相对位置、弹簧臂长度这三个属性共同决定。
  - 如果摄像机相对位置和弹簧臂相对位置都为(0,0,0)，弹簧臂长度就是摄像机到角色之间的距离。
- 制作第一人称游戏时，建议将此属性大小调整为0。
- 弹簧臂长度（即距离调整）示意图如下：
![](https://qn-cdn.233leyuan.com/online/K8FHXsvtcCTQ1723787855803.png)
![](https://cdn.233xyx.com/online/GwHaWgwltu481695279533730.gif)

#### 是否有摄像机碰撞
- 摄像机弹簧臂与其他物体存在碰撞效果，碰撞后会将摄像机位置前移，防止穿模或者玩家角色被遮挡。
- 可以理解为：如果有开启碰撞的物体挡在主视口的红色细线上，就会触发摄像机碰撞
- 开启摄像机碰撞
![](https://cdn.233xyx.com/1684475954182_709.gif)

#### 是否开启摄像机放缩距离
- 是否开启双指或鼠标滚轮放缩摄像机弹簧臂长度，默认开启。
- 为了满足玩家录制游戏视频时拉近到角色的需要，强烈推荐各第三人称视角项目开启这一功能；如果在运镜过程中或者某些游戏阶段时不希望玩家能双指放缩镜头，可以动态关闭此属性。
- 需要注意：由于双指缩放功能使用了Touch事件来计算与屏幕接触的手指的个数，而精确点击模式按钮（touchMethod=PreciseTap，建议仅适用于滚动框中的按钮）会与Touch事件有冲突造成Bug表现，所以推荐游戏中主HUD的按钮尽量使用普通点击模式（touchMethod=DownAndUp）
#### 放缩距离范围
- 用于来设置玩家双指/滚轮放缩摄像机弹簧臂长度的最大值和最小值，仅在当前摄像机弹簧臂长度处于摄像机放缩距离范围内时生效，默认值60~500。
#### 放缩距离输入比例
- 用于设置玩家双指/滚轮放缩摄像机弹簧臂长度的灵敏度。
- 摄像机放缩距离的效果示例：
<video controls src="https://cdn.233xyx.com/online/0v6ZxWPFon6V1704449477079.mp4"></video>


### 2.3 其他设置

#### 开启摄像机位置延迟
- 摄像机延迟是对摄像机跟随人物的运动、视角的旋转进行延迟、滞后，使得运镜更顺滑、更有代入感。开启摄像机位置延迟后，在人物位移时，摄像机跟随人物的运动会有延迟效果。
#### 开启摄像机位置延迟
- 控制人物在位移时，摄像机抵达目标位置的速度。低数值较慢(延迟高)，高数值较快(延迟低)，零为即时(无延迟)。
- 此外，延迟效果还受位置延迟最大距离影响（脚本中用maxLagDistance属性进行调整），例如下面几幅动图中，位置延迟最大距离为100(厘米）。

| **位置延迟速度=8**       | ![](https://cdn.233xyx.com/1684475954511_620.gif) |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **位置延迟速度=2**       | ![](https://cdn.233xyx.com/1684475955080_298.gif) |
| **位置延迟速度=0 或关闭** | ![](https://cdn.233xyx.com/1684475954511_919.gif) |

#### 开启摄像机旋转延迟
- 开启摄像机旋转延迟后，在手指/鼠标控制摄像机滑动区进行视角旋转时，摄像机旋转会有延迟效果。
#### 开启摄像机旋转延迟
- 控制视角在旋转时，摄像机抵达目标位置的速度。低数值较慢(延迟高)，高数值较快(延迟低)，零为即时(无延迟)。

| **旋转延迟速度=8**        | ![](https://cdn.233xyx.com/1684475954189_668.gif) |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **旋转延迟速度=2**        | ![](https://cdn.233xyx.com/1684475954897_433.gif) |
| **旋转延迟速度=0或关闭** | ![](https://cdn.233xyx.com/1684475954321_282.gif) |

#### 向上限制角度
- 摄像机向上旋转时的最大角度，防止旋转至角色模型下方，导致穿模效果。
- 范围：0~90°  数值越大，可旋转的角度越大。
#### 向下限制角度
- 摄像机向下旋转时的最大角度，防止旋转至角色模型上方，导致穿模效果。
- 范围：0~90°  数值越大，可旋转的角度越大。
- 限制角度示意图：
![](https://qn-cdn.233leyuan.com/online/CgsZ3qdPay931723787856421.jpg)

#### 是否开启物体透明
- 开启后，当摄像机与角色之间有其他障碍物体时，会让障碍物体变透明。
#### 物体不透明度
- 调整障碍物体变透明时的不透明度。
![](https://cdn.233xyx.com/online/ClrdpU2ddqOp1695279533731.gif)


## 如何通过API动态修改摄像机效果？


### 示例1：调整视场和摄像机位置以实现狙击枪瞄准效果

* 现在我们来制作一个FPS射击游戏中狙击枪的瞄准效果，在非瞄准状态下使用预置的FPS射击视角，摄像机能看到手持的狙击枪；进入瞄准状态后，摄像机显示瞄准镜中看到的画面，且不再能看到手持的狙击枪
* 因此，我们需要处理一下逻辑：
  * 将视场调小，来模拟瞄准镜中的画面
  * 将摄像机位置向前移动，使画面中不再出现狙击枪
  * 将射击摇杆的灵敏度调低，防止瞄准状态时调整瞄准方向过于灵敏
* 脚本示例：

```TypeScript
		//按下射击摇杆进入瞄准状态
		fireButton.onJoyStickDown.add(() => {
			//将视场调小，来模拟瞄准镜中的画面
			Camera.currentCamera.fov=70
			//将将摄像机位置向前移动，使画面中不再出现狙击枪
			let cameradata=Camera.currentCamera.localTransform
			cameradata.position.x+=100
			Camera.currentCamera.localTransform=cameradata
			//将射击摇杆的灵敏度调低，防止瞄准状态时调整瞄准方向过于灵敏
			fireButton.inputScale=(new Vector2(0.04, 0.03))
		});
		
		//松开射击摇杆发射子弹，并恢复到原有的TPS射击视角
		fireButton.onJoyStickUp.add(() => {
			Event.dispatchToLocal("FIRE_CLICK_Gun");
			//恢复预置的TPS射击视角，重置摄像机位置和视场
			Camera.currentCamera.preset=4
			//恢复原有的射击摇杆灵敏度
			fireButton.inputScale=(new Vector2(0.08, 0.06))
		});
```


### 示例2：动态切换摄像机的位置模式和朝向模式
* 可以通过调整positionMode和rotationMode这两条属性来动态动态切换摄像机的位置模式和固定模式
* 也可以启用控制器操作摄像机useControllerRotation来切换摄像机的朝向模式
* 当rotationMode=RotationControl时，此时弹簧臂的方向与弹簧臂相对旋转springArm.localTransform.rotation不是对应关系，而需要用控制器旋转（Player.getControllerRotation/Player.setControllerRotation ）来获取或者设置
* 脚本示例：

```TypeScript
import DefaultUI_generate from "./ui-generate/DefaultUI_generate";

@UIBind('')
export default class UIDefault extends DefaultUI_generate {

    /** 仅在游戏时间对非模板实例调用一次 */
    protected onStart() { 
        //设置能否每帧触发onUpdate
        this.canUpdate = false;


        //LocationFixed
        this.mStaleButton.onClicked.add(() => {
			Camera.currentCamera.positionMode=0
        })
        //LocationFollow
		this.mStaleButton_1.onClicked.add(() => {
			Camera.currentCamera.positionMode=1
        })

        //RotationFixed
        this.mStaleButton_2.onClicked.add(() => {
			Camera.currentCamera.rotationMode=0
		})
        //RotationFollow
        this.mStaleButton_3.onClicked.add(() => {
			Camera.currentCamera.rotationMode=1
		})
        //RotationControl
        this.mStaleButton_4.onClicked.add(() => {
			Camera.currentCamera.rotationMode=2
		})
    }
}
```

* 实现效果示例：

<video controls src="https://cdn.233xyx.com/1684475954572_044.mp4"></video>


### 示例3：实现多摄像机之间的切换
* 上文有提到，除了【对象管理器-世界对象】中有一个自带的无法被删除的摄像机对象，我们还可以从【资源库-游戏功能对象】中拖出或者在脚本中动态创建任意个摄像机对象，下面我们演示一下如何使用switch接口在多个摄像机对象之间自由切换
* 使用switch切换摄像机时，可以实现瞬间切换到新的摄像机，也可以使用编辑器提供的多种混合效果，完成匀速/变速的运镜效果
* 提示：各个摄像机对象及其弹簧臂的属性值都是独立的，如果想在游戏中实现多种摄像机效果变换时，可以考虑两种制作思路
  * 如果各种摄像机效果差别不大，我们可以使用同一个摄像机对象，通过修改属性来实现效果的切换；或者如果想要实现让摄像机挂载在不同的角色或模型上，可以用parent接口修改这一摄像机对象的父级（使用parent接口时要注意，如果设置为父级的是角色会保留之前的弹簧臂相对位置不变，弹簧臂世界位置将会发生变化；而如果设置为父级的是其他物体，则会使保持弹簧臂世界位置不变，弹簧臂相对位置会发生变化；如果想要切换parent前后保持弹簧臂相对位置不变，建议先存储弹簧臂相对位置的值，切换parent之后重新设置一下）
  * 如果各种摄像机效果差别较大，需要调整较多属性，我们可以创建多个摄像机对象，各个摄像机对象用于实现专门的效果，或者各自挂在不同的角色或模型上，然后通过switch接口来实现效果的切换
* 脚本示例：

```TypeScript
 @Component
 export default class Example_Camera_Switch extends Script {
     // 当脚本被实例后，会在第一帧更新前调用此函数
     protected onStart(): void {
         // 下列代码仅在客户端执行
         if(SystemUtil.isClient()) {
             // 获取当前摄像机
             let myCamera = Camera.currentCamera;
             let curCameraIndex = -1;
             // 在场景中随机创建5个摄像机
             let cameraArray = new Array<Camera>();
             for (let i = 0; i< 5;i++) {
                 let camera = GameObject.spawn("Camera") as Camera;
                 camera.worldTransform.position = new Vector(MathUtil.randomInt(-1000, 1000), MathUtil.randomInt(-1000, 1000),MathUtil.randomInt(0, 1000));
                 camera.worldTransform.rotation = new Rotation(MathUtil.randomInt(-90, 90), MathUtil.randomInt(-30, 30),MathUtil.randomInt(-150, 150));
                 cameraArray.push(camera);
                 camera.onSwitchComplete.add(() => {
                     console.log("当前摄像机序号 " + i);
                     curCameraIndex = i;
                 });
             }
             // 添加一个按键方法：按下键盘“1”，切换摄像机
             InputUtil.onKeyDown(Keys.One, () => {
                 console.log("Switch Camera");
                 let newCamera = (curCameraIndex + 1) % 5;
                 Camera.switch(cameraArray[newCamera], 5, CameraSwitchBlendFunction.Linear);
             });
             // 添加一个按键方法：按下键盘“2”，切换回默认摄像机
             InputUtil.onKeyDown(Keys.Two, () => {
                 console.log("Switch Default Camera");
                 Camera.switch(myCamera);
             });
         }
     }
 }
```
* 实现效果示例：
![](https://cdn.233xyx.com/online/2o8uyunB3uhI1697435647673.gif)

* 如果想要新创建一个挂在角色身上的摄像机对象并切换过去，可以参考以下示例代码：
```TypeScript
 @Component
 export default class Example_Camera_Switch extends Script {
     // 当脚本被实例后，会在第一帧更新前调用此函数
     protected onStart(): void {
         // 下列代码仅在客户端执行
         if(SystemUtil.isClient()) {
	        // 获取当前摄像机
	        let myCamera = Camera.currentCamera;
		//创建并且设置第二个摄像机，挂载在角色身上
		let camera = GameObject.spawn<Camera>("Camera") as Camera;
		camera.parent=Player.localPlayer.character
		camera.positionMode=1
		camera.rotationMode=2
		camera.springArm.useControllerRotation=true
		camera.springArm.length=500
		camera.fov=90

		// 添加一个按键方法：按下键盘“1”，切换摄像机
		InputUtil.onKeyDown(Keys.One, () => {
		    console.log("Switch Camera");
		    Camera.switch(camera);
		});
		// 添加一个按键方法：按下键盘“2”，切换回默认摄像机
		InputUtil.onKeyDown(Keys.Two, () => {
		    console.log("Switch Default Camera");
		    Camera.switch(myCamera);
		});
         }
     }
 }
```
