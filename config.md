# 编辑配置文件

本插件配置目录文件位于 `服务端根目录\plugins\JulySafe\config\config.yml` 。

本插件所有版本均使用 **UTF-8** 解码，请勿使用其他编码。推荐使用 `Visual Studio Code` 来编辑，请勿使用记事本编辑。

编辑完成后需要手动进行重载，重载指令：`/jsafe reload`。

# 模块开关

本插件所有功能均可自行开启和关闭。

```yaml
# 自动重启
auto_restart:
  enabled: false
  bossbar:
    color: YELLOW
    style: SOLID
  times: # 24小时制
    - '23:00:00'
  countdown_seconds: 60 # 倒计时时间
  before_commands: # 重启前执行的指令
    - 'save-all'
```

以上述配置为例：`enabled` 项为 `false`，即代表功能不开启；若 `enabled` 项为 `true`，即代表功能开启。

# 世界名过滤器

本插件部分模块需要指定世界名，世界名使用 [正则表达式](https://www.runoob.com/regexp/regexp-syntax.html) 进行匹配。

```yaml
entity_spawn_interval_limit:
  enabled: true # 是否开启
  worlds: # 世界正则表达式
    - '.*'
  intervals:
    '1':
      interval: 1000
      target:
        includes: # 包含
          '1':
            type: ENUM
            regexes:
              - 'ZOMBIE'
        excludes: {}
```

以上述配置为例：`worlds` 项使用 `.*` 作为匹配规则，即代表匹配所有世界。

# 时间单位

本插件除了 `entity_spawn_interval_limit` 中使用了**毫秒**单位，其他模块一律使用**秒**作为时间单位。

# 红石限制

配置例子如下：

```yaml
redstone_limit:
  enabled: true
  worlds:
    - '.*'
  checker:
    type: BLOCK_COUNTER # 方块计次检查器
    block_whitelist: # 方块白名单
      - 'DAYLIGHT_DETECTOR'
    reset_interval: 15 # 检测重置时间
    threshold: 10 # 60秒内不允许激活超过10次红石信号
    ban_duration: 120   # 封禁时间，秒
    notify_interval: 10 # 通知间隔
    below_tps: 21 # 低于 tps 将启用限制
```

参数详细解释：

* block_whitelist

  红石方块白名单，在该名单内的红石方块不被检测。

* reset_interval

  检测重置时间，以该项为一个检测周期，若在该检测周期内红石信号超过了阈值则封禁。

* threshold

  阈值

* ban_duration

  若超过了阈值，封禁持续的时间。

* notify_interval

  通知间隔

* below_tps

  低于该项 tps 将启用红石限制检测。

## 检查器

本插件提供了三大红石检查器，以使用不同的检测需求：

* BLOCK_COUNTER

  单方块红石计数检查器，若单个红石方块的运行次数在检测周期内超过了阈值，则执行封禁。

  该检查器适用于检测高频红石。

* CHUNK_RUNTIME

  区块红石运行时间检查器，若整个区块的红石方块运行时间在检测周期内超过了阈值，则执行封禁。

  该检查器适用于检测高低频红石。

* CHUNK_COUNTER

  区块红石技术检查器，若整个区块的红石方块运行次数在检测周期内超过了阈值，则执行封禁。

  该检测器不常用，误判较高。

# 执行器（executor）

 执行器用于执行模块任务，目前作用于以下模块：

* clean_entity
  
  清理实体
* clean_drop
  
  清理掉落物
* clean_crowed_entity
  
  清理区块密集实体
* auto_restart
  
  自动重启

本插件有三大执行器：

* 间隔执行器
  
  按间隔执行任务。
* 定时执行器
  
  在指定时间执行任务。
* 调度执行器
  
  一个个任务排队按间隔执行。

## 间隔执行器

### 参数

| 参数名   | 类型 | 解释               |
| -------- | ---- | ------------------ |
| interval | 整数 | 执行间隔，单位：秒 |

### 例子

```yaml
  executor:
    type: INTERVAL
    countdown:
      notifier:
        # 倒计时秒数
        seconds: '1-60'
        # 通知
        notification:
          type: TITLE # 倒计时类型
          # 参数
          properties:
            title: '&e即将清理生物'
            subtitle: '&c倒计时: ${current}秒'
    properties:
      interval: 300 # 5分钟
```

以上例子将 5分钟执行一次，并在倒计时（1-60秒时）使用 Title 进行倒计时提醒。

## 定时执行器

### 参数

| 参数名 | 类型     | 解释             |
| ------ | -------- | ---------------- |
| times  | 时间列表 | 24 小时制时间    |
| async  | 布尔型   | 是否使用异步线程 |

### 例子

```yaml
  executor:
    type: TIME
    properties:
      async: true # 使用异步线程
      times: # 24 小时制时间
        - '20:54:00'
        - '00:00:00'
    countdown:
      notifier:
        seconds: '1-60'
        notification:
          type: BOSSBAR
          properties:
            title: '&e重启倒计时 ${current}秒'
            color: YELLOW
            style: SOLID
```

## 调度执行器

倒计时执行器有个很大的问题：若使用 Message 会同时发送倒计时消息，这将很影响玩家体验，若使用 Title 发送倒计时消息，这会导致只显示一个 Title，因为 Title 只能同时显示一个。这时候就需要用到调度执行器进行统一调度，让任务一个个按优先级顺序循环运行。

调度器配置：

```yaml
# 任务调度器
scheduler:
  delay: 0 # 首次执行延迟
  execute_interval: 300 # 执行间隔，秒
```

### 参数

| 参数名   | 类型   | 解释   |
| -------- | ------ | ------ |
| priority | 整数型 | 优先级 |

### 例子

```yaml
  executor:
    type: SCHEDULER # 调度执行器
    # 参数
    properties:
      # 优先级
      priority: 1
    # 倒计时
    countdown:
      # 通知器
      notifier:
        # 倒计时秒数
        seconds: '1-60'
        # 通知
        notification:
          type: TITLE # 倒计时类型
          # 参数
          properties:
            title: '&e即将清理生物'
            subtitle: '&c倒计时: ${current}秒'
```

# 执行器倒计时

每个执行器都配置了一个倒计时模块，用于给玩家通知倒计时。

本插件提供了三种倒计时方式：

* Title
* BossBar
* Message

## 倒计时通知秒数

要使用倒计时通知必须指定倒计时通知秒数。

### 例子

```yaml
1,2,3,4,5,10,20,30,60
1-10,30,60
```

```yaml
xx:
    # 略
    # 倒计时
    countdown:
      # 通知器
      notifier:
        # 倒计时秒数
        seconds: '1-60'
        # 略
```

使用 `,` 来分隔秒数。

使用 `-` 来指定一个范围秒数，`a-b` 包含了 `[a, b]` 区间的所有秒数。



## 通知变量

| 变量名  | 解释           |
| ------- | -------------- |
| current | 当前倒计时秒数 |

倒计时提供了一些变量，通过 `${变量名}` 来使用变量。

## Title 通知

### 参数

| 参数名   | 默认值 | 类型   | 解释   |
| -------- | ------ | ------ | ------ |
| title    | 无     | 文本型 | 大标题 |
| subtitle | 无     | 文本型 | 小标题 |

### 例子

```yaml
xxx:
	# 略
    countdown:
      notifier:
        seconds: '1-60'
        notification:
          type: TITLE
          properties:
            title: '&e即将清理掉落物'
            subtitle: '&c倒计时: ${current}秒'
```

## BossBar 通知

### 参数

| 参数名        | 默认值     | 类型   | 解释                                                         |
| ------------- | ---------- | ------ | ------------------------------------------------------------ |
| title         | 无         | 文本型 | 标题                                                         |
| color         | BLUE       | 枚举   | 血条颜色，[样式列表](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/boss/BarColor.html) |
| style         | SOLID      | 枚举   | 血条样式，[样式列表](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/boss/BarStyle.html) |
| progress_type | INCREASING | 枚举   | 可用枚举：`INCREASING` 递增，`DECREASING` 递减               |

### 例子

```yaml
xxx:
	# 略
    countdown:
      notifier:
        seconds: '1-60'
        notification:
          type: BOSSBAR
          properties:
            title: '&e重启倒计时 ${current}秒'
            color: YELLOW
            style: SOLID
            progress_type: INCREASING
```

## Message 通知

### 参数

| 参数名  | 默认值 | 类型   | 解释 |
| ------- | ------ | ------ | ---- |
| message | 无     | 文本型 | 消息 |

### 例子

```yaml
xxx:
	# 略
    countdown:
      notifier:
        seconds: '1-60'
        notification:
          type: MESSAGE
          properties:
            message: '&c倒计时: ${current}秒'
```

## 关闭倒计时

若您不需要倒计时请显式进行关闭。

例子：

```yaml
  executor:
    type: INTERVAL
    countdown:
      notifier:
        enabled: false # 关闭
```

# 实体目标

实体目标通过包含和不包含准确判断实体是否为目标实体。

本插件以下模块用到了实体目标：

* clean_entity

  实体清理

* anti_entity_farm

  密集实体清理

## 例子

```yaml
  target:
    # 包含
    includes:
      '1':
        type: CLASS
        properties:
          classes:
            - 'Monster'
    # 除外
    excludes:
      '1':
        type: METADATA
        properties:
          keys:
            - 'MyPet'
            - 'NPC'
      '2':
        type: CUSTOM_NAME
        properties:
          regexes:
            - '.*'
      '3':
        type: ENUM
        properties:
          regexes:
            - 'ENDER_DRAGON'
            - 'ZOMBIE_VILLAGER'
```

目标分为两大块：

* includes

  被包含的匹配器
* excludes

  不被包含的匹配器

当被匹配者（实体，掉落物）同时匹配到两大目标（被包含，不被包含）时，算作**未匹配成功**。

# 掉落物目标

掉落物匹配器与实体匹配器的格式一致。

# 掉落物匹配器

## 枚举匹配器

该匹配器主要用于匹配一个或多个掉落物。

枚举就是类型，例如在 MC 里苹果的枚举是 `APPLE`，石头的枚举是 `STONE`。

### 格式

```yaml
type: ENUM
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

标准枚举名列表：https://hub.spigotmc.org/javadocs/spigot/org/bukkit/Material.html

**枚举严格区分大小写**。

### 样例

```yaml
type: ENUM
properties:
    regexes:
    - 'APPLE'
    - 'DIAMOND'
    - 'BLUE_.*'
```

以上匹配了苹果、钻石、所有蓝色的物品（床、羊毛等）。

## 附魔匹配器

### 格式

```yaml
type: ENCHANTMENT
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

标准附魔名列表：https://hub.spigotmc.org/javadocs/spigot/org/bukkit/enchantments/Enchantment.html

### 样例

```yaml
type: ENCHANTMENT
properties:
    regexes:
    - 'DAMAGE_ALL'
```

以上匹配了锋利附魔。

## lore 匹配器

### 格式

```yaml
type: LORE
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

### 样例

```yaml
type: LORE
properties:
    regexes:
    - '不清理'
```

以上匹配了包含 `不清理` lore 的物品。

## 物品名匹配器

### 格式

```yaml
type: CUSTOM_NAME
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

### 样例

```yaml
type: CUSTOM_NAME
properties:
    regexes:
    - '§[a-zA-Z0-9]已绑定'
```

这里匹配了包含已绑定lore（+颜色代码）的物品。

# 实体目标匹配器

## 枚举匹配器

该匹配器主要用于匹配一个或多个实体。

### 格式

```yaml
type: ENUM
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

标准枚举名列表：https://hub.spigotmc.org/javadocs/spigot/org/bukkit/entity/EntityType.html

### 样例

```yaml
type: ENUM
properties:
    regexes:
    - 'ZOMBIE.*'
    - 'PIG'
```

首先第一个正则是是 `ZOMBIE.*`，规则匹配到了 `ZOMBIE` 和 `ZOMBIE_VILLAGER` 类型，第二个参数 `PIG` 就是单个类型。

所以最终匹配到了僵尸、僵尸村民，猪。

## 类匹配器

如果要选择一类实体（如动物，怪物，Boss）要怎么做？一个个打实体类型是不是太麻烦了（这有好几十个），使用 Class 匹配器即可。

### 格式

```yaml
type: CLASS
properties:
    classes:                   
    - '类名1'
    - '类名2'
    - '...'
```

类名存在以下可用参数：

可用类型：https://hub.spigotmc.org/javadocs/spigot/org/bukkit/entity/Entity.html

**类名严格区分大小写。**

常用类：

* Monster

  怪物
* Animals

  动物
* Boss

  末影龙，凋零

### 样例

```yaml
type: CLASS
properties:
    classes:
    - 'Animals'
```

这里匹配了所有动物。

## 实体名匹配器

该匹配器主要用于匹配 Mod 实体。

### 格式

```yaml
type: NAME
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

## 自定义实体名匹配器

### 格式

```yaml
type: CUSTOM_NAME
properties:
    regexes:
    - '正则表达式1'
    - '正则表达式2'
    - '...'
```

### 样例

```yaml
type: CUSTOM_NAME
properties:
    regexes:
    - 'Zombie'
```

这里匹配了 `僵尸` 实体。

## Metadata 匹配器

该匹配器主要用于匹配实体自定义标签，不常用，一般用于匹配 MyPet 和 Citzens 的实体，使其不被误清除。

### 格式

```yaml
type: METADATA
properties:
    keys:
    - 'data1'
    - 'data2'
    - '...'
```

### 样例

```yaml
type: METADATA
properties:
    keys:
    - 'MyPet'
    - 'NPC'
```

这里匹配了 MyPet 实体和 Citzens 实体。
