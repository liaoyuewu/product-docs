# 角色编辑器服务

`AvatarEditorService`是一个包含角色编辑器相关功能的服务。它给开发者提供修改玩家角色形象、请求玩家外观库存、购买外观商品和请求外观商品目录的方法。该服务仅在客户端生效。
## 默认角色编辑器

### 平台入口

游戏界面左上角寻找“去装扮”按钮，点击按钮打开角色编辑器。该按钮可以通过接口动态控制显隐以便开发者自行决定是否接入平台默认角色编辑器。使用示例如下所示：

```TypeScript
// 设置“去装扮”按钮隐藏
AvatarEditorService.setAvatarEditorButtonVisible(false);
```

![img](https://arkimg.ark.online/1730860472575-52.jpeg)

### 打开角色编辑器

`AvatarEditorService`提供了一个默认角色编辑器，开发者可以通过下列两个方法来打开/关闭它：

- `AvatarEditorService.asyncOpenAvatarEditorModule()`
- `AvatarEditorService.asyncCloseAvatarEditorModule()`

方法仅支持在客户端调用。

下列示例代码在玩家客户端给键盘1和键盘2添加绑定函数，分别控制打开/关闭角色编辑器组件：

```TypeScript
InputUtil.onKeyDown(Keys.One, () => {
    AvatarEditorService.asyncOpenAvatarEditorModule();
});

InputUtil.onKeyDown(Keys.Two, () => {
    AvatarEditorService.asyncCloseAvatarEditorModule();
});
```

![img](https://arkimg.ark.online/1730876686239-1.png)

### 形象展示区

打开后会加载页面如上图所示。角色编辑器界面默认会在UI顶层显示，需注意可能会挡住游戏UI。此外过程中会操作玩家当前角色并修改朝向模式和摄像机配置（但并不影响其它逻辑）。默认角色编辑器左侧为角色形象展示区（展示的就是玩家角色），支持快捷切换为二次元男/女和平台形象如下图所示。

| **二次元女** | **二次元男** | **平台形象** |
| :--------------- | :----------- | :----------- |
| ![img](https://arkimg.ark.online/1730877109573-8.png) | ![img](https://arkimg.ark.online/1730877120258-11.png) | ![img](https://arkimg.ark.online/1730877159496-20.png) |

中间栏展示角色当前已穿戴的外观（不含默认形象外观），点击“×”会卸载当前穿戴，对应部位恢复为默认形象外观。点击“保存”按钮后，玩家角色会在退出角色编辑器时应用编辑器内外观形象，否则恢复为进入角色编辑器之前的形象。需注意保存形象时如果含有未持有/限定商品则会跳转支付，根据用户支付结果来决定形象是否保存。
| **商城外观形象** | **游戏外观形象** | **下单支付** |
| :--------------- | :----------- | :----------- |
| ![img](https://arkimg.ark.online/1730877402821-1.png) | ![img](https://arkimg.ark.online/1730877313742-28.png) | ![img](https://arkimg.ark.online/1730860472570-7-1730877288639-23.png) |



### 外观商城

角色编辑器右侧是是商品页面，售卖平台市场中修改角色形象的外观商品。商品页面一共三个分类分别是“捏脸”、“商城”和“我的”，分别展示不同商品数据。

- 捏脸

主要售卖面部外观商品如眼睛、眉毛、妆容等。完整分类如下所示
| **眼睛** | **眉毛** | **妆容** |
| :--------------- | :----------- | :----------- |
| ![img](https://arkimg.ark.online/1730877468513-4.png) | ![img](https://arkimg.ark.online/1730877478492-7.png) | ![img](https://arkimg.ark.online/1730877486306-10.png) |

- 商城

主要售卖服饰和饰品（挂件），其中套装服饰是单件商品的集合
| **服饰** | **饰品** |
| :--------------- | :----------- |
| ![img](https://arkimg.ark.online/1730877575773-13.png) | ![img](https://arkimg.ark.online/1730877584052-16.png) |

- 我的

主要展示玩家仓库中持有的各类服饰和饰品资源

![img](https://arkimg.ark.online/1730877622080-19.png)


商品页面中按照商品分类使用平铺图标方式展示商品效果。商品图标下方标明商品价格。点击图标会修改右侧角色形象进行试穿方便用户预览效果。点击右上角“×”按钮退出角色编辑器。

![img](https://arkimg.ark.online/1730877634927-22.png)

### 角色编辑器事件

默认角色编辑器在运行时可以通过`AvatarEditorService.avatarServiceDelegate`来监听商城中各种行为事件，开发者可以绑定函数来针对抛出的不同事件做相应的处理。事件列表如下所示。

| 事件描述           | 事件名称    | 参数1类型               | 参数描述                                                     | 参数2类型 | 参数描述                   | 参数3类型 | 参数描述             |
| :----------------- | :---------- | :---------------------- | :----------------------------------------------------------- | :-------- | :------------------------- | :-------- | :------------------- |
| 打开界面           | AE_OnOpen   | -                       | -                                                            | -         | -                          | -         | -                    |
| 关闭界面           | AE_OnQuit   | -                       | -                                                            | -         | -                          | -         | -                    |
| 组件界面内按钮点击 | AEE_OnClick | -                       | -                                                            | -         | -                          | -         | -                    |
| 组件购买商品       | AEM_OnPay   | number                  | 结果code200: 订单支付成功408: 请求超时409: 处理下单回调报错410: 处理支付回调报错501: 余额不足502: 暂未开放购买503: 参数类型错误504: 用户取消505: 轮询失败，未支付成功506: 该版本不支持 payType507: 未知异常，包括但不限于网络连接失败其它：服务端返回的错误码，包括但不限于：下单，预支付，轮询等接口的报错 | number    | 本次要下单的商品价格总数   | string[]  | 本次要下单的商品信息 |
| 形象保存           | AE_OnSave   | mw.CharacterDescription | 形象外观数据                                                 | string    | 对应外观体型类型的基础姿态 |           |                      |

下列示例代码展示了在打开/关闭默认角色编辑器界面时禁止/启用角色移动：

```TypeScript
mw.AvatarEditorService.avatarServiceDelegate.add((eventName: string, ...params: unknown[]) => {
    switch (eventName) {
        case "AE_OnQuit":
            Player.localPlayer.character.setStateEnabled(CharacterStateType.Running, true);
            break;
        case "AE_OnOpen":
            Player.localPlayer.character.setStateEnabled(CharacterStateType.Running, false);
            break;
    }
});
```

## 物品信息

### 数据项

`AvatarEditorService`提供了一系列商品信息的查询接口，便于开发者利用这些信息制作自己的角色编辑器和外观商城。外观商品的数据结构如下所示：

- **itemId 物品ID**

物品ID是角色编辑器针对市场中外观资源定义的唯一ID。

- **itemType 物品分类 &  tagId 标签ID**

平台市场中的物品有多种类型，开发者通过`itemType`可以区分物品类型做到分类展示。角色外观物品每种类型又分为二次元男/女风格（其余风格目前已不维护），开发者通过`tagId`可以更近一步做到男女外观物品的区分，针对不同性别展示不同商品。此外请求数据标签也是通过`tagId`进行索引。换装物品可以获取角色外观描述`character.description`并设置资源ID。

| **ItemType** | 类型名称  | 示例                                                         | 换装接口                                                     | **tagId** | 风格             |
| :----------- | :-------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------- | :--------------- |
| 5            | 上衣      | ![img](https://arkimg.ark.online/1730878014227-25.png)       | advance.clothing.upperCloth                                  | 13        | 二次元女上衣     |
| -            | -         | ![image-20241106152716447](https://arkimg.ark.online/image-20241106152716447.png) | -                                                            | 17        | 二次元男上衣     |
| 6            | 下衣      | ![image-20241106152738046](https://arkimg.ark.online/image-20241106152738046.png) | advance.clothing.lowerCloth                                  | 14        | 二次元女下衣     |
| -            | -         | ![image-20241106152750016](https://arkimg.ark.online/image-20241106152750016.png) | -                                                            | 18        | 二次元男下衣     |
| 7            | 手套      | ![image-20241106152851617](https://arkimg.ark.online/image-20241106152851617.png) | advance.clothing.gloves                                      | 15        | 二次元女手套     |
| -            | -         | ![image-20241106152843589](https://arkimg.ark.online/image-20241106152843589.png) | -                                                            | 19        | 二次元男手套     |
| 8            | 鞋子      | ![image-20241106152918414](https://arkimg.ark.online/image-20241106152918414.png) | advance.clothing.shoes                                       | 16        | 二次元女鞋       |
| -            | -         | ![image-20241106152928772](https://arkimg.ark.online/image-20241106152928772.png) | -                                                            | 20        | 二次元男鞋       |
| 9            | 前发      | ![image-20241106153026434](https://arkimg.ark.online/image-20241106153026434.png) | advance.hair.frontHair                                       | 2         | 二次元女前发     |
| -            | -         | ![image-20241106153010252](https://arkimg.ark.online/image-20241106153010252.png) | -                                                            | 7         | 二次元男前发     |
| 10           | 后发      | ![image-20241106153103585](https://arkimg.ark.online/image-20241106153103585.png) | advance.hair.backHair                                        | 3         | 二次元女后发     |
| -            | -         | ![image-20241106153112501](https://arkimg.ark.online/image-20241106153112501.png) | -                                                            | 8         | 二次元男后发     |
| 11           | 瞳孔样式  | ![image-20241106153211049](https://arkimg.ark.online/image-20241106153211049.png) | advance.makeup.coloredContacts.style.pupilStyle              | 159       | 二次元女瞳孔样式 |
| -            | -         | ![image-20241106153204403](https://arkimg.ark.online/image-20241106153204403.png) | -                                                            | 163       | 二次元男瞳孔样式 |
| 12           | 瞳孔贴花  | ![image-20241106153253601](https://arkimg.ark.online/image-20241106153253601.png) | advance.makeup.coloredContacts.decal.pupilStyle              | 162       | 二次元瞳孔贴花   |
| 13           | 睫毛      | ![image-20241106153317022](https://arkimg.ark.online/image-20241106153317022.png) | advance.makeup.eyelashes.eyelashStyle                        | 6         | 二次元女睫毛     |
| -            | -         | ![image-20241106153324854](https://arkimg.ark.online/image-20241106153324854.png) | -                                                            | 11        | 二次元男睫毛     |
| 14           | 眉毛      | ![image-20241106153508309](https://arkimg.ark.online/image-20241106153508309.png) | advance.makeup.eyebrows.eyebrowStyle                         | 5         | 二次元女眉毛     |
| -            | -         | ![image-20241106153450188](https://arkimg.ark.online/image-20241106153450188.png) | -                                                            | 10        | 二次元男眉毛     |
| 15           | 口红      | ![image-20241106153558997](https://arkimg.ark.online/image-20241106153558997.png) | advance.makeup.lipstick.lipstickStyle                        | 33        | 女唇妆           |
| -            | -         | ![image-20241106153651890](https://arkimg.ark.online/image-20241106153651890.png) | -                                                            | 34        | 男唇妆           |
| 16           | 腮红      | ![image-20241106153730811](https://arkimg.ark.online/image-20241106153730811.png) | advance.makeup.blush.blushStyle                              | 29        | 女腮红           |
| -            | -         | ![image-20241106153709529](https://arkimg.ark.online/image-20241106153709529.png) | -                                                            | 30        | 男腮红           |
| 18           | 整体发型  | ![image-20241106154042601](https://arkimg.ark.online/image-20241106154042601.png) | advance.hair.frontHair/advance.hair.backHair                 | 168       | 女整体发型       |
| -            | -         | ![image-20241106154049790](https://arkimg.ark.online/image-20241106154049790.png) |                                                              | 167       | 男整体发型       |
| 19           | 套装      | ![image-20241106154144924](https://arkimg.ark.online/image-20241106154144924.png) | -                                                            | 138       | 女套装           |
| -            | -         | ![image-20241106154136990](https://arkimg.ark.online/image-20241106154136990.png) | -                                                            | 139       | 男套装           |
| 26           | 眼影      | ![image-20241106154213954](https://arkimg.ark.online/image-20241106154213954.png) | advance.makeup.eyeShadow.eyeshadowStyle                      | 31        | 女眼妆           |
| -            | -         | ![image-20241106154221315](https://arkimg.ark.online/image-20241106154221315.png) | -                                                            | 32        | 男眼妆           |
| 28           | 挂件_左手 | ![image-20241106154243895](https://arkimg.ark.online/image-20241106154243895.png) | advance.slotAndDecoration.slot[10].decoration                | 71        | 二次元           |
| 29           | 挂件_背部 | ![image-20241106154258929](https://arkimg.ark.online/image-20241106154258929.png) | advance.slotAndDecoration.slot[13/14].decoration             | 74        | 二次元           |
| 30           | 挂件_头部 | ![image-20241106154321479](https://arkimg.ark.online/image-20241106154321479.png) | advance.slotAndDecoration.slot[1].decoration                 | 70        | 二次元           |
| 31           | 挂件_耳部 | ![image-20241106154333174](https://arkimg.ark.online/image-20241106154333174.png) | advance.slotAndDecoration.slot[2/3].decoration               | 73        | 二次元           |
| 32           | 挂件_面部 | ![image-20241106154348186](https://arkimg.ark.online/image-20241106154348186.png) | advance.slotAndDecoration.slot[6].decoration                 | 75        | 二次元           |
| 36           | 面部彩绘  | ![image-20241106154414002](https://arkimg.ark.online/image-20241106154414002.png) | -                                                            | 150       | 二次元           |
| 37           | 上高光    | ![image-20241106154432492](https://arkimg.ark.online/image-20241106154432492.png) | advance.makeup.coloredContacts.highlight.upperHighlightStyle | 160       | 二次元           |
| 38           | 下高光    | ![image-20241106154440088](https://arkimg.ark.online/image-20241106154440088.png) | advance.makeup.coloredContacts.highlight.lowerHighlightStyle | 161       | 二次元           |
| 39           | 挂件_臀部 | ![image-20241106154452526](https://arkimg.ark.online/image-20241106154452526.png) | advance.slotAndDecoration.slot[19].decoration                | 76        | 二次元           |
| 40           | 挂件_肩部 | ![image-20241106154500862](https://arkimg.ark.online/image-20241106154500862.png) | advance.slotAndDecoration.slot[8/9].decoration               | 78        | 二次元           |

- **content 内容**

在众多物品当中有些物品内含一组商品例如面部彩绘、整体发型、套装、饰品等，它们自身作为一件物品却由多件物品组成，其中还包含很多细致的设置参数例如颜色，偏移，插槽等。`content`属性正是详细描述这类组合物品的一个json格式的文本数据，通过解析该份数据正确的将物品设置到角色外观中。

- **commodityId 商品ID**

一件物品不一定是商品，例如存在会员权益物品，限定免费物品等。商品是具备标价并在市场上可以购买的物品。当一件物品是商品时通过`commodityId`属性对它进行唯一标识。它也是调用下单购买接口时需要传入的唯一商品ID。

- **price 价格**

每件商品有自己的标价，以平台货币作为单位。

- **weight 排序权重** 

物品按照稀有程度，受欢迎程度等多维度指标在后台存在一个权重值，开发者可以利用它对物品进行排序展示。

- **acquiredFrom 获取方式**

物品有不同的获取方式：免费商品、VIP权益、商城购买。不同的获取方式对应不同的操作，需要使用正确的操作才能将物品成功保存至玩家仓库。开发者利用`acquiredFrom`属性可以提示用户，只有acquiredFrom = 3的物品才能作为商品使用`Purchase`接口购买。

| **AcquiredFrom** | **获取方式** |
| :--------------- | :----------- |
| 1                | 免费商品     |
| 2                | VIP权益      |
| 3                | 商城购买     |

- **prefabGuid 资源ID**

物品本质上还是一种换装资源，在游戏中成功的应用到角色身上需要使用资源ID并调用相关换装接口。

- **iconGuid 图标ID**

物品在商城中的展示通常需要图标，而针对物品后台提供了高质量的外观资源贴图可以使开发者直观的展示换装资源的表现和效果。部分物品并没有专门配置图标ID，当“图标ID” = "0"或者与“图标ID” = “资源ID”相等时，说明不存在独立图标。此时可以直接请求资源本身图标来显示，如下所示：

```TypeScript
let image: Image = this.uiWidgetBase.findChildByPath('RootCanvas/Iamge') as Image;
image.imageInfo.setByAssetIcon("资源ID", AssetIconSize.Icon_64px);
```

### 请求物品数据

请求物品数据接口存在频率限制：每隔100ms可以调用一次。此外接口限制在客户端使用，因此客户端执行换装后，开发者需要使用`Character.syncDescription()`进行外观数据的同步。

`AvatarEditorService`提供4种请求物品数据的方法分别是：

1. **按照标签请求物品数据**

`AvatarEditorService.asyncGetCommodityListByTag()`可以通过物品的类别ID请求不同分类下的物品数据。通常游戏中进行分类展示物品的时候会单独请求一个分类，这样既可以减少数据查询和传输的时间，也更加适合脚本逻辑的编写。

下列示例代码绑定了一个按键方法，当按下小键盘按键1时在客户端会执行逻辑：请求“二次元女上衣”分类的物品数据并按照权重降序排列（权重相同时按照物品ID排列）。最后遍历物品数据并打印到输出窗口。

```TypeScript
class ItemData {
    itemId: number;
    itemType: any;
    prefabGuid: string;
    weight: number;
    iconGuid: string;
    content: string;
    commodityId: string;
    price: number;
    acquiredFrom: number[];
}

InputUtil.onKeyDown(Keys.NumPadOne, async () => {

    let result = await AvatarEditorService.asyncGetCommodityListByTag([13]);

    if(result.errorCode == 200) {
        let itemList: ItemData[] = [];
        itemList = result.data;
        itemList.sort((a, b) => {
            if (a.weight != b.weight)
                // 按权重降序排列
                return b.weight - a.weight
            else
                // 权重相同的情况下，按 itemId 降序排列
                return b.itemId - a.itemId
        });
        for (const element of itemList) {
            console.log("\n物品ID ", element.itemId, "\n商品ID ", element.commodityId, "\n价格 ", element.price, "\n权重 ", element.weight, "\n资源ID ", element.prefabGuid, "\n图标ID ", element.iconGuid);       
        }

    } else {
        console.log(result.message);
    }
});
```

![img](https://arkimg.ark.online/1730860472572-40.png)

通过进一步组织数据并搭配UI界面则可以实现下图类似效果：

![img](https://arkimg.ark.online/1730860472572-41.png)

2. **请求玩家持有物品数据**

`AvatarEditorService.asyncGetMyItemsListByTag()`可以通过物品的类别ID请求平台玩家持有的不同分类下的物品数据。通常在角色编辑界面中需要展示当前玩家的物品仓库，而该接口可以帮助开发者访问玩家持有物品的数据并展示。“按照标签请求物品数据”的示例代码只需更换请求接口，即可获取不同数据。

```TypeScript
let result = await AvatarEditorService.asyncGetMyItemsListByTag([13]);
```

![img](https://arkimg.ark.online/1730860472572-42.png)

3. **请求资源对应的物品数据**

`AvatarEditorService.asyncGetCommodityByAssetIds()`可以通过资源ID请求对应的物品数据。有时候我们可能需要将A玩家当前穿戴的物品分享给B玩家，或者展示玩家当前穿戴的物品信息。此时需要开发者可以根据玩家换装数据中资源ID属性使用接口请求对应物品信息。示例如下所示，下图展示当前玩家穿戴物品。

```TypeScript
let result = await AvatarEditorService.asyncGetCommodityByAssetIds(["340302"]);
```

![img](https://arkimg.ark.online/1730880622322-28.gif)

4. **请求物品ID对应的物品数据**

`AvatarEditorService.asyncGetCommodityListByItemIds()`可以通过物品ID请求对应的物品数据。通常商品数据较大，脚本中可能只会缓存对应的物品ID，通过物品ID我们也可以请求对应的物品数据。

```TypeScript
let result = await AvatarEditorService.asyncGetCommodityListByItemIds([5305]);
```

### 解析请求结果

请求物品数据返回的结果是`CommodityListObj`，它是包含一个商品数据列表、错误码、错误信息的数据结构。

**CommodityListObj参数**

| 参数              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| data: any         | 商品数据列表类型为any，需要用户自行转化成对应的数据对象然后使用。转化示例如下：`let result = await AvatarEditorService.asyncGetCommodityListByTag([13]); let itemList: ItemData[] = []; itemList = result.data; for (const element of itemList) {    console.log("\n物品ID ", element.itemId);        }` |
| errorCode: number | 请求返回的错误码，开发者可以根据结果决定是否重新请求数据，结构如下：`{    */**请求成功\*/*    Success = 200, }` |
| message: string   | 返回的错误信息，可以用于文本提示。                           |

## 购买支付

### 货币余额

购买角色编辑器物品需要消耗玩家余额，通常在商城页面需要对玩家余额不足的情况进行提示，玩家根据自己的选择进行充值或者放弃本次购买。`AvatarEditorService`提供玩家余额的查询接口和重置接口便于开发者处理购买过程中出现的余额不足情况。

![img](https://arkimg.ark.online/1730860472572-44.png)

在购买行为发生之前，首先需要判断当前运行环境是否支持支付行为。平台包含的支付行为一共有两种：积分和代币。通过以下两个接口可以针对支付行为的合法性进行判断：

```TypeScript
// 判断当前环境是否支持代币支付
let result = await AvatarEditorService.asyncGetCashPayEnabled();

// 判断当前环境是否支持积分支付
let result = await AvatarEditorService.asyncGetPointPayEnabled();
```

在商城页面或者下单页面通常需要展示玩家余额。`AvatarEditorService.getAccountBalance()`提供查询用户余额的功能。由于数据需要后端提供，搭配使用`AvatarEditorService.onAccountBalanceUpdated`委托事件来获取请求结果。

**BalanceInfo参数**

| 参数          | 说明     |
| :------------ | :------- |
| coin: number  | 代币余额 |
| point: number | 积分余额 |

下列示例先给余额委托事件绑定函数打印余额至控制台，然后绑定按键方法，按下键盘1获取用户余额：

```TypeScript
AvatarEditorService.onAccountBalanceUpdated.add((balance) => {
    console.log("Player's points: " + balance.point);
});

InputUtil.onKeyDown(Keys.One, () => {
    AvatarEditorService.getAccountBalance();
});
```

### 下单发货

当用户选好商品后可以使用`AvatarEditorService.placeOrder()`下单商品。开发者需要传入一个商品列表（支持一次购买多个商品）。商品ID可以从物品数据中`commodityId`获取。

**CommodityInfo参数**

| 参数                | 说明                              |
| :------------------ | :-------------------------------- |
| commodityId: string | 商品Id                            |
| number: number      | 商品数量，暂时只支持1不可重复购买 |

![img](https://arkimg.ark.online/1730860472572-45.png)

整个下单过程包含下单商品、余额校验、支付商品、生成订单等。因此在每个关键阶段和关键节点我们会执行回调函数`placeOrderResult()`便于开发者做针对性处理。

**placeOrderResult参数**

| 参数            | 说明                                                         |
| :-------------- | :----------------------------------------------------------- |
| status: number  | 订单状态`{    status = 200, // 订单支付成功    status = 408, // 请求超时    status = 409, // 处理下单回调报错    status = 410, // 处理支付回调报错    status = 501, // 余额不足    status = 502, // 暂未开放购买    status = 503, // 参数类型错误    status = 504, // 用户取消    status = 505, // 轮询失败，未支付成功    status = 506, // 该版本不支持支付    status = 507, // 未知异常，包括但不限于网络连接失败 }` |
| msg: string     | 订单详细信息                                                 |
| orderId: string | 订单号                                                       |


| **充值入口** | **充值弹窗** | **支付入口** | **支付成功** |
| :--------------- | :----------- | :----------- | :----------- |
| ![img](https://arkimg.ark.online/1730860472572-46.png) | ![img](https://arkimg.ark.online/1730860472572-47.png) | ![img](https://arkimg.ark.online/1730860472572-48.png) |![img](https://arkimg.ark.online/1730860472572-49.png) |

订单支付成功后`AvatarEditorService.onOrderDelivered`事件会被触发，标志着本次购买正式完成。如果中途失败则会返回一个状态告知开发者。

**ShipOrderResponse参数**

| 参数            | 说明                                                 |
| :-------------- | :--------------------------------------------------- |
| orderId: string | 订单号                                               |
| userId: string  | 用户ID                                               |
| status: boolean | 发货成功返回true购买已拥有的商品/服务器错误返回false |
| message: string | 发货的具体信息                                       |

下列示例代码展示给`onOrderDelivered`事件绑定函数执行打印发货结果，成功时打印订单信息。给UI按钮绑定点击方法，从输入框中获取商品ID并进行下单，下单成功时打印订单ID。

```TypeScript
AvatarEditorService.onOrderDelivered.add((res) => {
    let s = "";
    if(res.status == true) {
        s += "发货成功\n";
        s += ("订单号 " + res.orderId + "\n");
        s += ("用户号 " + res.userId);
    } else {
        s += ("发货失败 " + res.message)
    }
    output.text = s;
})

const purchaseBtn = this.uiWidgetBase.findChildByPath('RootCanvas/Button_Purchase') as Button
purchaseBtn.onPressed.add(() => {
    console.error("chargeBtn.onPressed");
    let commodity: CommodityInfo;
    commodity.commodityId = input.text;
    commodity.number = 1;
    let s = "";
    AvatarEditorService.placeOrder([commodity], (status, msg, id) => {
        if(status == 200) {
            s += "下单成功\n";
            s += "订单ID " + id;
        }
        output.text = s;
    });
});
```

