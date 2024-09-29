# 内存存储[内测]

::: tip **阅读本文预计 20 分钟**
**本文概述了 内存存储 的使用方法，协助您在游戏中快速读取数据。**
:::

## 1.什么是内存存储？

- 内存存储（Memeory Storage）是一种高吞吐量（低延迟、访问速度、并行计算、实时更新等）的数据服务，结合数据存储（Data Storage）可以使得游戏逻辑结构更加完整，优化用户的使用体验。
- **内存存储**和**数据存储**的区别：

| - | **内存存储（Memeory Storage）** | **数据存储（Data Storage）** |
| - | - | - |
| 速度 | 快速访问低延迟 | 涉及磁盘读写，更慢 |
| 持久 | 内存存储就像是你的大脑短期记忆，它主要用于存放那些变化快、不需要长期保存的数据 | 数据存储更像是你的大脑长期记忆，它主要用于存放不经常变化的数据 |
| 场景 | 适合用在需要快速响应的地方，比如在线游戏中的玩家排名、实时交易和拍卖等 | 适用于需要数据持久化和历史记录保留的应用场景，比如用户的账户信息、历史交易记录等 |

## 2. 如何使用内存存储？

::: tip
由于数据存储在服务器中，庞大的用户会造成服务压力，所以通过给予**特定的数据结构**，并自动分片到服务器中，从而减缓压力，达成负载均衡。
故而，我们在使用内存存储时，通过使用场景，只能使用两种数据结构**排序映射**和**队列**。
:::

| 中文示例 | 英文示例 |
| - | - |
| ![](https://qn-cdn.233leyuan.com/athena/online/0b74addc832948c6bbe2be7fb92c35be_387363532.webp) | ![](https://qn-cdn.233leyuan.com/athena/online/159ad9bd43ce4cbdaa8e6875d1d57157_387363533.webp) |

- 排序映射（SortedMap）：
  - **特点**：排序映射就像一个有序的书架，每本书（数据项）都有一个特定的位置（排序键），书架上的所有书都是按照某种顺序（比如书名的字母顺序）排列的。
  - **使用场景**：当你需要按照一定的顺序来组织和访问数据时，比如全球排行榜，排序映射可让你快速找到特定范围内的数据，比如获取排名前10的玩家，或者根据分数快速插入和更新排名。。
- 队列（Queue）：
  - **特点**：队列就像一个排队的队伍，人们（数据项）按照先来后到的顺序排队，新来的人（新数据）总是站在队伍的末尾，而队伍前面的人（旧数据）会先得到服务。
  - **使用场景**：当你需要按照数据到达的顺序来处理它们时，比如基于技能的匹配，用户信息（如段位信息）保存在队列中，系统会根据用户进入队列的顺序来进行匹配。
 
## 3. 排序映射

>如下会通过编写一个排行榜的形式介绍排序映射的接口。

### 3.1 创建排序映射

```ts
@Component
export default class LeaderBoard extends Script {

    // 存储leaderboard对象
    leaderboard: MemoryStorageExtend.MemoryStorageSortedMap = null;

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {
        this.leaderboard = MemoryStorageExtend.MemoryStorage.getSortedMap("leaderboard");
    }
}
```

### 3.2 添加项/覆盖项

```ts
@mw.RemoteFunction(mw.Server)
async setDataFunction(){
    // key: 玩家ID
    // value: 到达段位的时间
    // timeout: 有效时间，以秒为单位，3000秒后该数据自动消除
    // sortKey: 排序键，根据字典序排序
    await this.setData("player1", "rankDate:2024-01-01", 3000, "A_3");
    await this.setData("player2", "rankDate:2024-01-02", 3000, "E_3");
    await this.setData("player3", "rankDate:2024-01-03", 3000, "C_4");
}

/**
 * 设置排行榜数据
 * @param leaderboard 排行榜对象
 */
@mw.RemoteFunction(mw.Server)
async setData(key: string, value: any, timeout: number, sortKey?: number|string): Promise<void> {
    // 在这里调用方法，存储返回的code
    let result = await this.leaderboard.asyncSetData(key, value, timeout, sortKey);
    
    // 通过回调查看成功与否
    if (result == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Data set successfully for ${key}`);
    } else {
        console.error(`Failed to set data for ${key}, code:`, result);
    }
}
```

排序不是根据value排序，而是使用排序键排序，如下图中是升序获取数据，返回的单个数据分为了4个部分，Data0代表排名，player7代表key，rankDate:2024-01-01代表value，-999代表sortkey。
排序的四个规则：
- 所有数据中，无论升序还是降序，都是先排序数字，再排序字符串，没有排序键的放在最后
- Data0到Data4的排序键为number，按照数字大小排序
- Data5到Data17的排序键为string，使用字典序+Ascii码排序
- Data18到Data19的排序键为null，放在最后，不进行排序

![](https://qn-cdn.233leyuan.com/athena/online/5c6193e284a645b6ab20c7bbf7f5d5e9_387395977.webp)

### 3.3 获取单个项

```ts
@mw.RemoteFunction(mw.Server)
async getData(key: string): Promise<void> {
    // 调用方法返回数据，数据中含有value代表返回的数据，code代表错误码
    let result = await this.leaderboard.asyncGetData(key);

    if (result.code == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Get Data successfully for ${key}:`, result.value);
    } else {
        console.error(`Failed to retrieve data for ${key}, code:`, result.code);
    }
}
```

### 3.4 删除单个项

```ts
@mw.RemoteFunction(mw.Server)
async removeData(key: string): Promise<void> {
    let result = await this.leaderboard.asyncRemoveData(key);

    if (result == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Data removed successfully for ${key}`);
    } else {
        console.error(`Failed to remove data for ${key}, code:`, result);
    }
}
```

::: tip
如果想要删除多个项，可以用3.5获取项后遍历删除。
:::

### 3.5 获取多个项

```ts
@mw.RemoteFunction(mw.Server)
async getRangeData(): Promise<void> {
    // 调用方法返回的数据含有一个包含所有返回数据的array和错误码code
    // 第一个bool代表是否为升序，第二个number代表返回多少个数据
    let result = await this.leaderboard.asyncGetRangeData(false, 20);
    
    // 还可以通过如下方法，添加获取数据的上下限
    // let lowerSortKey = 0;
    // let upperSortKey = "B";
    // let result = await this.leaderboard.asyncGetRangeData(false, 20, lowerSortKey, upperSortKey);
    
    if (result.code == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log("Get Range Data successfully:");
        // 遍历print出数据
        for (let i = 0; i < result.array.length; i++) {
            console.log(`Data ${i}:`, result.array[i].key, result.array[i].value, result.array[i].sortKey);
        }
    } else {
        console.error("Failed to retrieve data, code:", result.code);
    }
}
```

### 3.6 获取项排名

```ts
@mw.RemoteFunction(mw.Server)
async getRank(key: string, isAscending: boolean): Promise<void> {
    let result = await this.leaderboard.asyncGetRank(key, isAscending);

    if (result.code == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Get Rank successfully for ${key}:`, result.rank, isAscending ? "ascending" : "descending");
    } else {
        console.error(`Failed to retrieve rank for ${key}, code:`, result.code);
    }
}
```

## 4. 队列

> 如下会通过编写一个进入游戏排队系统的形式介绍队列的接口。

### 4.1 创建队列

```ts
@Component
export default class QueuingSystem extends Script {
    playersQueue: MemoryStorageExtend.MemoryStorageQueue = null;
    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {
        // 第一个参数为
        this.playersQueue = MemoryStorageExtend.MemoryStorage.getQueue("playersQueue", 20);
    }
}
```

::: tip
注意到创建队列的时候，队列名后面一个参数20（如果您不输入，那么默认为30）。
这代表队列中的项目被Read时的隐藏时间，根本原因是队列项被Read并不会立即消除，而是需要继续调用Remove才可删除项。
所以，当你Read出一个项时，该项在20秒内为你所有，其他人暂时不可以读出该项。若您在20秒内没有删除该项，那么20秒后它就会被放回队列中的原本位置，并且你再无法调用Remove删除它，同时其他人可以读出该项。
:::

### 4.2 添加队列项

```ts
/**
 * 添加玩家到队列中
 * @param playerName 玩家名
 */
@mw.RemoteFunction(mw.Server)
async addPlayerToQueue(playerName: string): Promise<void> {
    // 入参：项名称，过期时间，优先级
    let result = await this.playersQueue.asyncAddData(playerName, 600, 0);

    if (result == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Player ${playerName} added to queue`);
    } else {
        console.error(`Failed to add player ${playerName} to queue, code:`, result);
    }
}
```

::: tip
优先级的作用是什么？
优先级代表该项在队列中的位置，优先级越高的越先出队，如下图所示。
:::

![](https://qn-cdn.233leyuan.com/athena/online/2495e6c35d6e467b996666a25308c9dd_387397263.webp)

### 4.3 读出项

```ts
lastReadResult: MemoryStorageExtend.QueueReadDataResult = null;

/**
 * 从队列中读取玩家
 * @param count 读取数量
 */
@mw.RemoteFunction(mw.Server)
async readPlayerFromQueue(count: number): Promise<void> {
    this.lastReadResult = await this.playersQueue.asyncReadData(count, false, 0);

    if (this.lastReadResult.code == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        // 遍历读取的玩家
        for (const value of this.lastReadResult.values) {
            console.log(`Player ${value} read from queue`);
        }
    } else {
        console.error(`Failed to read player from queue, code:`, this.lastReadResult.code);
    }
}
```

读出项的API有三个参数，分别为：输出项的个数，当数量不够是否全部读出，数量不够的等待时间。
可以参照如下图片，得出队列读出项的内容。

| 中文示例 | 英文示例 |
| - | - |
| ![](https://qn-cdn.233leyuan.com/athena/online/fcbeebeb1dc747ce868ca94d5a2e6fc8_387397535.webp) | ![](https://qn-cdn.233leyuan.com/athena/online/2620c4d522a2403c885b4b1f6716de72_387397534.webp) |

### 4.4 删除（读出的）项

读出项之后，会返回一个指向你读出项的指针，使用该指针为入参调用删除数据API，即可删除读出的数据。

```ts
lastReadResult: MemoryStorageExtend.QueueReadDataResult = null;

/**
 * 从队列中删除玩家
 * @param playerName 玩家名
 * @param id 数据指针
 */
@mw.RemoteFunction(mw.Server)
async removePlayerFromQueue(): Promise<void> {
    let result = await this.playersQueue.asyncRemoveData(this.lastReadResult.id);

    if (result == MemoryStorageExtend.MemoryStorageResultCode.Success) {
        console.log(`Player removed from queue`);
    } else {
        console.error(`Failed to remove player from queue, code:`, result)
    }
}
```
