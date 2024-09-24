# 寻路系统
::: tip **阅读本文预计 10 分钟**
**本文概述了如何在编辑器中,使用寻路区域功能对象实现角色自动寻路的效果.**
:::


## 什么是寻路系统

> 寻路系统包含了寻路区域功能对象和动态寻路功能，在关卡中通过寻路区域功能对象绘制出一个可供角色或人形对象按自定义要求进行移动行为的区域，并可以通过动态寻路在游戏运行时修改可通行的区域范围。



## 如何使用寻路区域

- **step.1**

在编辑器左侧【游戏功能对象】选项中,找到【寻路区域】,拖拽到主视口,即可完成创建.

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://qn-cdn.233leyuan.com/online/QrmRaldqFbF41724657600630.png)|![](https://qn-cdn.233leyuan.com/online/3sNY4YEVYf761724657621146.png)|

编辑器主视口中绿色区域部分为可自动导航区域,通过右上方显示菜单功能,可打开/关闭主视口寻路区域的显示与隐藏.

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://qn-cdn.233leyuan.com/online/XghYTCIKk7dd1724657640219.png)|![](https://qn-cdn.233leyuan.com/online/3bHeNZB5fKge1724657650309.png)|

设置参数以需要使用寻路功能的对象为参照，比如默认大小的角色或 NPC 使用寻路时，寻路参数可以直接使用默认值，以保证角色或 NPC 对象导航时不会被阻挡。如果是体形较大或较小的对象使用寻路，可以适当调整胶囊半径、高度等参数，使寻路区域计算更加精准。

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://qn-cdn.233leyuan.com/online/oEyBlEflZZMG1724657659125.png)|![](https://qn-cdn.233leyuan.com/online/tYQSqykywDeM1724657673025.png)|

- **使用技巧**
根据场景的实际使用环境,寻路区域参数,可以让寻路效果更加平滑.例如适当增大胶囊体半径参数，可以让角色在转角导航时更新平滑,避免贴紧墙壁移动的现象.


- **step.2**

寻路区域被创建后，会自动分配到对象列表中的寻路区域分组内，通过属性面板可以修改寻路区域属性，使用相对缩放属性来改变寻路区域的大小。
::: warning **Precautions**
**建议每个场景中的所有寻路区域的总范围保持在400 Km * 400Km * 80Km以内,防止因寻路区域过大会造成寻路区域失效的情况.**
:::

::: danger **Deprecated**
**请勿修改寻路区域的相关旋转属性,以免出现寻路计算异常情况.**
:::


- **step.3**

寻路区域可以进行高度判断，需要向高处寻路时，请调整 Z 轴缩放属性。
| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://cdn.233xyx.com/online/47ys91FrEhFd1723774305112.jpg)|![](https://cdn.233xyx.com/online/DmFv9FDuvaAP1723774305632.jpg)|




## 如何让主角（Player）使用寻路功能向目标位置移动

- **step.4**

创建一个寻路区域【参见**如何使用寻路区域**】

- **step.5**

创建一个脚本，使用 navigateTo()方法控制角色进行自动寻路

```ts
@Component
export default class NewScript extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {

        let player = Player.localPlayer;  //获取角色
        let loc = new mw.Vector(1600, 0, 250);  //设定一个目标点

        //使用navigateTo()将角色移动到目标点
        InputUtil.onKeyDown(mw.Keys.One, () => {
            Navigation.navigateTo(player.character, loc, 10, () => {
                console.log(`寻路成功`);
            }, () => {
                console.log(`寻路失败`);
            });
        });

        //使用stopNavigateTo()函数，可以将指定的对象停止寻路
        InputUtil.onKeyDown(mw.Keys.Two, () => {
            console.log(`停止角色寻路}`);
            Navigation.stopNavigateTo(player.character);
        });
    }
}
```
<video controls src="https://cdn.233xyx.com/athena/online/91b057aac6904312b4fbd3e6ab1540ce.mp4"></video>


## 如何让人形对象（NPC）使用寻路持续跟随目标移动

- **step.6**

创建一个寻路区域【参见**如何使用寻路区域**】

- **step.7**

在场景中创建两个角色NPC，将其中一个角色设置为客户端
| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://cdn.233xyx.com/online/uiBbYCV7uaTR1693879253848.png)|![](https://cdn.233xyx.com/online/Zo8xdqkdsoqA1723774306166.jpg)|


- **step.8**

创建一个脚本，在脚本中使用follow()方法控制NPC跟随主角进行自动寻路

```ts

@Component
export default class NewScript1 extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {

        let player = Player.localPlayer;
        let NPCforC = await mw.GameObject.asyncFindGameObjectById("216613A7") as mw.Character;  //通过对象ID获取到客户端NPC

        //客户端NPC使用follow()开始跟随角色进行移动
        if (SystemUtil.isClient()) {
            if (Navigation.follow(NPCforC, player.character)){
                console.log(`在寻路区域内找到目标，开始跟随`);
                Navigation.follow(NPCforC, player.character, 10, () => {
                    console.log(`跟随到了目标，跟随成功`);
                }, () => {
                    console.log(`无法找到目标，跟随失败`);
                });
    
                this.serverFollow(player.userId);
            }else{
                console.log(`无法找到目标，跟随失败`);
            }
        }

        //开始执行跟随后，使用stopFollow()函数，将客户端NPC停止寻路功能，双端NPC需要在服务器端调用stopFollow()函数；
        InputUtil.onKeyDown(mw.Keys.Two, () => {
            console.log(`停止客户端NPC跟随}`);
            Navigation.stopFollow(NPCforC);
        });
    }

    @mw.RemoteFunction(mw.Server)
    serverFollow(id: string) {
        let player = Player.getPlayer(id); //在服务器端获取到主角；
        let NPCforSC = mw.GameObject.findGameObjectById("1CDC00EC") as mw.Character; //通过对象ID获取到双端NPC
        Navigation.follow(NPCforSC, player.character, 10, () => {
            console.log(`跟随成功`);
        }, () => {
            console.log(`跟随失败`);
        });
    }
}
```
<video controls src="https://cdn.233xyx.com/athena/online/f77582cdade543159194004d74600a17.mp4"></video>



## 动态修改寻路数据方法

动态寻路开启后,可以通过游戏运行时动态修改寻路导航数据，用于制作门、桥等有开关状态的寻路地区。
在编辑器菜单栏[设置]功能中增加了[寻路设置]属性,可以设置是否开启动态寻路功能。

> 关闭动态寻路后,寻路区域数据在项目发布时即构建完成,不会在游戏启动时消耗性能.

> 开启动态寻路后,会在游戏启动时对场景中的寻路区域进行构建,场景地图越大、地形碰撞越复杂构建耗时越高.以OPPO A57 手机为例，构建 100 * 100 大小地形寻路数据，耗时约 0.3 秒；


## 动态修改寻路数据方法

- **step.9**
在[世界设置]-[寻路设置]中打开动态寻路

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://wstatic-a1.233leyuan.com/productdocs/static/Cly0bNOUuo8uWYxcEthcmhRGnpg.png)|![](https://cdn.233xyx.com/online/X9oEPmetRCAT1723774306688.jpg)|

- **step.10**
在场景中设置两条寻路路径，并设置目标点

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://cdn.233xyx.com/online/cbcxXAu8ebtP1693892552089.png)|![](https://cdn.233xyx.com/online/sbQzIjIqQ8am1723774307188.jpg)|

- **step.11**
在资源库中搜索[寻路动态修饰区]，放置在路面，将[寻路动态修饰区]区域类型设置为[无效的]。

| 中文示例   | 英文示例 |
| ------ | ---------------------------- |
|![](https://cdn.233xyx.com/online/gT39sUpj4ZQZ1693892552089.png)|![](https://cdn.233xyx.com/online/hJ3v6uoOBH8b1723774307682.jpg)|

- **step.12**
创建一个脚本，用来控制[寻路动态修饰区]的区域类型状态。

```ts
@Component
export default class NewScript2 extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {

        let player = Player.localPlayer;
        let targetPos = new mw.Vector(1800,250,250); //设置一个目标点
        let modifierVolume =await mw.GameObject.asyncFindGameObjectById("352509AA") as mw.NavModifierVolume; //通过对象ID找到寻路动态修饰区

        InputUtil.onKeyDown(mw.Keys.One,()=>{
            Navigation.navigateTo(player.character,targetPos)
        });

        InputUtil.onKeyDown(mw.Keys.Two,()=>{
            modifierVolume.areaClass = mw.NavModifierType.Default //将寻路动态修饰区类型设置为默认可通行；
        })
    }
}
```
使用控制台命令 -show Navigation打开寻路区域显示
在寻路动态修饰区类型为无效时，主角通侧方道路进行寻路，到达了目标点。在改变寻路动态修饰区类型为默认可通行后，再次寻路，主角使用了距离较近的路线到达了目标点。
<video controls src="https://cdn.233xyx.com/athena/online/cd4a63204d304b48aace55bd7ffcb81c.mp4"></video>

