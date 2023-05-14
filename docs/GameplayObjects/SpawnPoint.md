# 初生点对象
::: info
**阅读本文预计 5 分钟**

在项目中使用【初生点】来确定玩家角色生成位置和朝向。一般游戏中使用初生点来控制玩家刷新的位置，例如出现在第一关的场景起点，或者分布于地图的各处。
:::
# 初生点对象

【初生点对象】是在项目中决定玩家角色初次生成或重新生成位置与朝向的对象。每个项目创建时都会自带一个【初生点对象】，你可以在【对象管理器】中找到它：BP_PlayerStart。此外你也可以在【游戏功能对象】栏目中找到【初生点对象】，并将它拖入场景中。当场景中存在多个【初生点对象】时，玩家角色会在其中随机选择一个作为生成或者刷新的位置。

![img](https://arkimg.ark.online/1684046700004-15.webp)![img](https://arkimg.ark.online/1684046700003-1.webp)

# 创建初生点

## 通过放置资源创建：

【初生点对象】本身作为一个游戏对象可以存在于游戏场景中。你可以从【本地资源库】中找到【游戏功能对象】栏，在游戏功能对象分类中，找到【初生点对象】并拖入【场景】或者【对象管理器】后，会自动生成一个新的初生点。选中初生点可以修改它的位置和旋转（仅z轴）。

1. 在【本地资源库】中找到【初生点对象】

![img](https://arkimg.ark.online/1684046700003-2.webp)

2. 将对象拖入到场景中或者【对象管理器】下

![img](https://arkimg.ark.online/1684046700003-3.webp)

3. 在右侧【对象管理器】中【对象】栏找到对应的【初生点对象】并自定义位置和旋转（仅z轴）。

![img](https://arkimg.ark.online/1684046700003-4.webp)![img](https://arkimg.ark.online/1684046700003-5.webp)

## 通过脚本创建：

通过脚本你也可以在游戏运行时通过【资源库】中的资源ID动态生成一个【初生点对象】来使用。【初生点对象】的资源ID为“PlayerStart”。在【工程内容】下的脚本目录中新建一个脚本文件，将脚本拖入【对象管理器】中【对象】栏。选中脚本进行编辑，将下列示例代码替换脚本中的`onStart`方法：异步生成一个【初生点】对象，关闭双端同步，位置为默认（0，0，0），旋转为默认（0，0，0），缩放倍数为默认（1，1，1）。打印生成【初生点】对象的guid。

```TypeScript
protected async onStart(): Promise<void> {
    if(SystemUtil.isServer()) {
        let spawnPoint = await Core.GameObject.asyncSpawn({guid: "PlayerStart", replicates: false}) as Gameplay.PlayerStart;
        console.log("spawnPoint 的guid是 " + spawnPoint.guid);
    }
}
```

此处我们也可以通过`spawn`接口生成，但是需要将【初生点对象】拖入【优先加载栏】或者将【初生点对象】进行【预加载】来保证生成后我们不需要等待资源下载而导致后续代码失效。

![img](https://arkimg.ark.online/1684046700003-6.webp)

```TypeScript
// 预加载资源，将下列代码粘贴到脚本中的onStart方法之前
@Core.Property()
preloadAssets: string = "PlayerStart"；
```
::: tip
需要注意的是【初生点对象】只存在于服务端，是一个单服务端游戏对象。【对象管理器】中对象会标明自己的网络状态。由于客户端不存在【初生点对象】，在脚本中你需要在服务端才能获取和生成【初生点对象】。
:::
# 自定义初生点对象

【初生点对象】的属性将决定玩家角色刷新时的位置和朝向：

玩家角色的位置：Location

玩家角色的朝向：Rotation（z轴）

在【对象管理器】中【对象】栏找到对应的【初生点对象】，选中后我们可以查看它的属性面板，通过属性面板我们可以修改【初生点对象】的坐标和旋转（仅z轴）。一般来说【初生点对象】不需要在脚本中操作，当游戏运行时玩家角色会随机在场景中选择一个初生点作为刷新的位置。

![img](https://arkimg.ark.online/1684046700003-7.webp)

## 初生点位置

玩家生成的位置是由【初生点对象】的位置决定的，你可以直接在【对象管理器】中【对象】栏选中【初生点对象】，在【属性面板】中找到【变换】，改变初生点的【位置】属性，就能改变玩家生成的位置。

![img](https://arkimg.ark.online/1684046700004-8.webp)

## 初生点旋转

玩家生成的朝向是直接由初生点的朝向决定的，你可以【对象管理器】中【对象】栏选中【初生点对象】，在【属性面板】中找到【变换】，改变初生点的【旋转】属性，就能改变玩家生成的位置。同时【初生点对象】的朝向在场景中会通过一个蓝色小箭头进行提示：

![img](https://arkimg.ark.online/1684046700004-9.webp)![img](https://arkimg.ark.online/1684046700004-10.gif)

::: tip

【初生点对象】其实并没有自己的私有属性，它的位置属性和旋转属性都是是继承自父类`GameObject`。【初生点对象】的【旋转】属性中X、Y的值不可修改，这是因为角色修改X，Y旋转会产生不可预料的问题。【初生点对象】的【缩放】属性也不可修改。

:::

# 使用初生点对象

## 获取初生点对象

### 【对象管理器】中【对象】栏下的【初生点对象】：

**使用`asyncFind`接口通过【初生点对象】的GUID去获取：**

1. 选中【初生点对象】后右键点击【复制对象ID】获取它的GUID。注意区分对象ID与资源ID的区别。

![img](https://arkimg.ark.online/1684046700004-11.webp)

1. 在脚本的`onStart`方法中添加下列代码：代码将异步查找ID对应的对象并以【初生点对象】进行接收。

```TypeScript
if(SystemUtil.isServer()) {
    let spawnPoint = await Core.GameObject.asyncFind("06D5DBFC") as Gameplay.PlayerStart;
    console.log("spawnPoint 的guid是 " + spawnPoint.guid);
}
```

**使用脚本挂载的方式进行获取：**

1. 将脚本挂载到【初生点对象】下方，此时会弹出脚本网络状态提示，点击确认即可

![img](https://arkimg.ark.online/1684046700004-13.webp)![img](https://arkimg.ark.online/1684046700004-14.webp)

2. 在脚本的onStart方法中添加下列代码：代码获取脚本挂载的对象并以【初生点对象】进行接收

```TypeScript
if(SystemUtil.isServer()) {
    let spawnPoint = this.gameObject as Gameplay.PlayerStart;
}
```

### 动态生成的【初生点对象】：

将下列示例代码添加到脚本的`onstart`方法中：示例代码在客户端往`asyncSpawn`接口（中传入初生点的资源ID“PlayerStart”异步生成了一个对应的【初生点对象】。

```TypeScript
if(SystemUtil.isServer()) {
    let spawnPoint = await Core.GameObject.asyncSpawn({guid: "PlayerStart", replicates: false}) as Gameplay.PlayerStart;
    console.log("spawnPoint 的guid是 " + spawnPoint.guid);
}
```
