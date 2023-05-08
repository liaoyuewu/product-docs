**阅读本文预计 5 分钟**

使用【空锚点】来解决场景搭建和对象挂载中由于对象的锚点固定导致根据锚点进行的旋转，缩放无法满足需求的情况。

# 空锚点对象

通常情况下资源库中的对象锚点都是固定的，而以锚点为基础执行对象变换经常不满足需求。【空锚点】提供用户去自定义对象锚点位置的功能，利用【空锚点】对象本身作为对象的锚点来使用。而且通过**自动居中，顶点吸附和手动偏移**3种偏移方式，提升【空锚点】作为父对象时在编辑状态下的操作便捷性。【空锚点】在工程内作为父对象存在时，【场景】内显示所有子对象所产生的的包围盒。你可以在【本地资源库】中的【游戏功能对象】栏中找到【空锚点】。

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164080-15.webp)

# 创建空锚点

## 通过放置资源创建：

【空锚点】对象本身作为一个游戏对象可以存在于游戏场景中。你可以从【本地资源库】中将【空锚点】拖入【场景】或者【对象管理器】来自动生成一个【空锚点】对象。

1. 在【本地资源库】中找到【空锚点】

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164078-1.webp)

1. 将对象拖入到【场景】中或者【对象管理器】下

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164078-2.webp)

1. 在右侧【对象管理器】中【对象】栏找到对应的【初生点对象】并自定义锚点变换和锚点偏移。

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164078-3.webp)![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164078-4.webp)

## 通过脚本创建：

通过脚本你也可以在游戏运行时通过【资源库】中的资源ID使用`asyncSpawn`接口动态生成一个【空锚点】对象来使用。【空锚点】的资源ID为“Anchor”。在【工程内容】下的脚本目录中新建一个脚本文件，将脚本拖入【对象管理器】中【对象】栏。选中脚本进行编辑，将下列示例代码替换脚本中的`onStart`方法：异步生成一个【空锚点】对象，打开双端同步，位置为默认（0，0，0），旋转为默认（0，0，0），缩放倍数为默认（1，1，1）。打印生成【初生点】对象的guid。

```TypeScript
protected async onStart(): Promise<void> {
    if(SystemUtil.isServer()) {
        let anchor = await Core.GameObject.asyncSpawn({guid: "Anchor", replicates: true}) as Gameplay.PlayerStart;
        console.log("anchor guid " + anchor.guid);
    }
}
```

此处我们也可以通过`spawn`接口生成，但是需要将【空锚点】拖入【优先加载栏】或者将【空锚点】进行【预加载】来保证生成后我们不需要等待资源下载而导致后续代码失效。

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164078-5.webp)

```TypeScript
// 预加载资源，将下列代码粘贴到脚本中的onStart方法之前
@Core.Property()
preloadAssets: string = "Anchor"；
```

设定锚点偏移并不会改变【空锚点】对象实际的Transform值。但是锚点偏移会影响执行变换时【空锚点】对象的枢轴点。

# 自定义空锚点偏移

【空锚点】对象的偏移将决定执行变换时【空锚点】对象的枢轴点。

**居中**：执行变换时以子对象包围盒的中心作为枢轴点进行移动，旋转，缩放。

![img](D:\产品手册图片\1683513164078-6.gif)

**顶点**：执行变换时以子对象包围盒的顶点作为枢轴点进行移动，旋转，缩放。

![img](D:\产品手册图片\1683513164078-7.gif)

**自定义偏移**：执行变换时以【空锚点】偏移后位置作为枢轴点进行移动，旋转，缩放。

![img](D:\产品手册图片\1683513164078-8.gif)

# 使用空锚点

锚点偏移只作用于编辑态，运行态【空锚点】执行变换时仍然以自身实际值为准。在代码中空锚点只有普通父对象的作用。

## 获取空锚点对象

### 【对象管理器】中【对象】栏下的【初生点对象】：

**使用****`asyncFind`****接口通过【初生点对象】的GUID去获取：**

1. 选中【空锚点】对象后右键点击【复制对象ID】获取它的GUID。注意区分对象ID与资源ID的区别。

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164079-9.webp)

1. 将【属性面板】中【基础属性】类中的【静态状态】取消勾选，静态对象不执行脚本逻辑。

![img](https://meta.feishu.cn/space/api/box/stream/download/asynccode/?code=YmY3NjJjMzVhMDlmMThiZjRmNWEyNDVmMjNlNmEzMTZfcExabDQzSWs4WUlrdDE5NDduTkNiMEVtYUM5NkdJM3ZfVG9rZW46U0J0cWJPdTBMb0ZvQ2F4VnJwZWNkaXlwbjJnXzE2ODM1MTMxNjA6MTY4MzUxNjc2MF9WNA)![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164079-11.webp)

1. 在脚本的onStart方法中添加下列代码：代码将异步查找ID对应的对象并以【空锚点】对象进行接收。

```TypeScript
if(SystemUtil.isServer()) {
    let anchor = await Core.GameObject.asyncFind("3819014E") as Gameplay.Anchor;
    console.log("anchor guid " + anchor .guid);
}
```

**使用脚本挂载的方式进行获取：**

1. 选中【空锚点】对象，在属性面板中将静态状态取消

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164079-12.webp)![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164079-13.webp)

1. 将脚本挂载到【空锚点】对象下方

![img](%E2%80%8Bhttps://arkimg.ark.online/1683513164079-14.webp)

1. 在脚本的onStart方法中添加下列代码：代码获取脚本挂载的对象并以【空锚点】对象进行接收

```TypeScript
let anchor = this.gameObject as Gameplay.Anchor ;
```

### 动态生成的【初生点对象】：

下列示例代码替换脚本中的`onStart`方法：示例代码在客户端往`asyncSpawn`接口（中传入【空锚点】的资源ID“Anchor”异步生成了一个对应的【空锚点】对象。

```TypeScript
protected async onStart(): Promise<void> {
    if(SystemUtil.isServer()) {
        let anchor = await Core.GameObject.asyncSpawn({guid: "Anchor", replicates: true}) as Gameplay.PlayerStart;
        console.log("anchor guid " + anchor.guid);
    }
}
```
