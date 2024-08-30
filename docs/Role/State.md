# 角色基础状态

**阅读本文大概需要 15 分钟**

本文概述了角色的基础状态都有哪些，每种基础状态的切换与表现效果，以及可以利用角色的基础状态做哪些功能。

## 基础状态介绍

角色基础状态指的是角色在使用某种基础能力的情况下，使角色做出相应的动作效果，并且可以让角色在一段时间内处于一种稳定的状态，这种状态就是角色的基础状态。举个例子，角色在地面移动的时候，角色会播放跑步的动作，这个时候，角色就是处于地面移动状态。

## 角色基础状态

目前角色基础状态有9个，玩家可以通过接口切换基础状态，也可以通过跳跃等基础能力，自动切换到对应的基础状态。

| 中文名称  | 枚举名称 | 枚举序号  | 应用说明 |
| ----- | ----- | ----- | ----- |
| 无 | None | 0 | 取消角色的复杂移动模式，采用简单移动模式状态 |
| 地面移动状态 | Running | 1 | 角色在地面移动时的状态 |
| 飞行状态 | Flying | 2 | 角色在空中飞行时的状态 |
| 游泳状态 | Swimming | 3 | 角色在游泳时的状态 |
| 跳跃状态 | Jumping | 4 | 角色在执行跳跃功能时的状态 |
| 自由落体状态 | Freefall | 5 | 角色在空中下落时的状态 |
| 着陆状态 | Landed | 6 | 角色在自由落体后，接触到地面时的状态 |
| 布娃娃状态 | Ragdoll | 7 | 角色在布娃娃状态时的状态 |
| 下蹲状态 | Crouching | 8 | 角色在下蹲时的状态 |


### 地面移动状态

状态定义：角色在地面的情况下，移动或静止时的状态效果。

状态说明：地面移动状态是大多数角色切换状态后的最终状态，也是角色最为基础的状态。比如角色切换为自由落体状态后，然后经过一段时间会接触到地面触发着陆状态，最后切换为地面移动状态。我们也可以动作主动切换地面移动状态，来重置角色当前的基础状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：地面移动状态
chara.changeState(CharacterStateType.Running)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/qg4Uh1ypYUsr1706169923337.mp4"></video>

### 飞行状态

状态定义：角色在忽略重力的情况下，在空中移动或静止时的状态。

状态说明：飞行状态是属于相对独立的角色状态，角色切换为飞行状态后，会自动播放飞行状态下的移动和待机动画。我们需要主动切换为其他的角色基础状态，才会取消角色的飞行状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：飞行状态
chara.changeState(CharacterStateType.Flying)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/9yhUEvmkWiht1706169923337.mp4"></video>

### 游泳状态

状态定义：角色在水中情况下，移动或静止时的状态效果。

状态说明：游泳状态是属于相对独立的角色状态，角色切换为游泳状态后，会自动播放游泳状态下的移动和待机动画。我们需要主动切换为其他的角色基础状态，才会取消角色的游泳状态。

| 中文示例                                                   | 英文示例                                                   |
| ---------------------------------------------------------- | ---------------------------------------------------------- |
| ![](https://arkimg.ark.online/image-20240830172102586.png) | ![](https://arkimg.ark.online/image-20240830172154676.png) |

注意：我们逻辑对象中提供了游泳区域的逻辑对象，进入游泳区域时，玩家会自动切换为游泳状态。离开游泳区域时，玩家会自动切换为自由落体状态，最终变为地面移动状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：游泳状态
chara.changeState(CharacterStateType.Swimming)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/rkL653HIoT7q1706169923337.mp4"></video>

### 跳跃状态

状态定义：角色在执行跳跃功能时的状态效果。

状态说明：跳跃状态并不是一个持续状态，属于过度状态，角色在执行跳跃功能时，将会切换为跳跃状态。在达到设定的跳跃高度后，会自动切换为自由落地状态，最后着陆，进入到地面移动状态。

注意：角色主动切换跳跃状态时，角色会直接执行跳跃功能。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：跳跃状态
chara.changeState(CharacterStateType.Jumping)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/9782PJilWK241706169923337.mp4"></video>

### 自由落体状态

状态定义：角色在受到重力影响下，从空中下落的状态效果。

状态说明：自由落体状态并不是一个持续状态，属于过度状态，角色在通过高空坠落时，将会切换为自由落体状态。角色在碰触到地面后会触发着陆状态，最后进入到地面移动状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：自由落体状态
chara.changeState(CharacterStateType.Freefall)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/ZFOExHVY8aAC1706169923337.mp4"></video>

### 着陆状态

状态定义：角色在高空下落并接触到地面后的瞬时状态。

状态说明：着陆状态并不是一个持续状态，属于过度状态，角色在通过在空中接触到地面上时，将会切换为着陆状态，并播放着陆动画，最后进入到地面移动状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：着陆状态
chara.changeState(CharacterStateType.Landed)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/tHjpo0uG0FpH1706169923337.mp4"></video>

### 布娃娃状态

状态定义：角色在不受玩家控制，且处于物体模拟的状态效果。

状态说明：角色在受到外力被击飞或倒地时，模拟现实中的真实效果，而产生的角色状态，该状态下角色不受玩家控制，并且角色模型会受到外力影响，产生一些物理效果。布娃娃状态是属于相对独立的角色状态，角色切换为布娃娃状态后，会自动切换为布娃娃效果。我们需要主动切换为其他的角色基础状态，才会取消角色的布娃娃状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：布娃娃状态
chara.changeState(CharacterStateType.Ragdoll)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/eVmTGdoWrtSV1706169923337.mp4"></video>

### 下蹲状态

状态定义：角色在执行下蹲功能时的状态效果。

状态说明：下蹲状态是属于相对独立的角色状态，角色切换为下蹲状态后，会自动播放下蹲状态下的移动和待机动画。我们需要主动切换为其他的角色基础状态，才会取消角色的下蹲状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：下蹲状态
chara.changeState(CharacterStateType.Crouching)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/LIRla2nc2hiR1706169923337.mp4"></video>

### 无

状态定义：角色将取消复杂移动逻辑，需要用户自定义动作状态和移动效果的状态。

状态说明：无状态是属于相对独立的角色状态，角色切换为无状态后，角色会关闭复杂移动逻辑，玩家需要自定义动作姿态和移动效果。我们需要主动切换为其他的角色基础状态，才会取消角色的无状态。

示例脚本：

```ts
//获取玩家角色
let chara = Player.localPlayer.character
//如果是老项目，需要重新设置动作资源
chara.loadStance("216081").play();
//设置角色状态：无状态
chara.changeState(CharacterStateType.None)
```

效果图：

<video controls src="https://cdn.233xyx.com/online/PhwW8hR9gE7j1706169923337.mp4"></video>

## 启用/禁用状态

我们提供了启用和禁用基础状态的功能，如果我们禁用了某种基础状态后，角色将无法切换到该禁用的基础状态下，角色会维持在切换前的上一个基础状态效果。

示例脚本：

```ts
//禁用角色的地面移动状态
chara.setStateEnabled(CharacterStateType.Running,false);
//启用角色的地面移动状态
chara.setStateEnabled(CharacterStateType.Running,true);
```

## 获取状态和切换状态回调

我们提供了获取角色当前状态，以及角色在切换状态时的回调，方便角色在切换状态时增加一些游戏逻辑。

示例脚本：

```ts
//获取当前玩家角色
Player.asyncGetLocalPlayer().then((player) => {
    //角色状态改变时，回调角色之前状态和现在状态
    chara.onStateChanged.add((pre, curr)=>{
        //打印信息
        console.log(`pre: ${pre} curr: ${curr}`);
    })
});
```
