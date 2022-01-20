# 2021/02/18 v2.0.3

* 修复了倒计时时重载导致 BossBar 异常的 Bug。
* 修复了 `entity_spawn_interval_limit` 模块失效的 Bug。
* 修复了阳光传感器导致 `redstone_limit` 模块误判的 Bug。
* 增加了绕过 `chat_limit` 模块的权限（`JulySafe.ChatLimit.Bypass`）。

# 2021/05/04 v2.0.4

* 修复了 `quick_shop_bug_fix` 模块在 1.13.2 版本以下的 Bug。
* 增加了 `NAME` 实体匹配器用于匹配 Mod 生物。

# 2021/05/08 v2.0.5

* 绕过了 QuickShop(作者: Ghost_chu) 检查。

# 2021/07/17 v2.0.6

* 修复部分服务端清理垃圾和清理实体模块BossBar无法正常工作的Bug。

# 2021/08/13 v2.0.7

* 倒计时类型支持消息和 BossBar。
* 增加命令限制模块。

# 2022/01/12 v3.0.0-beta

* 整体代码重构

* 增加了 Executor 执行器
  
  你可以在实体清理，掉落物清理，密集实体清理，自动重启模块中自由选择三种执行器
* 倒计时模块增加至3种可选的提示方式：
  * Title
  * BossBar
  * Message
* 管理员现在可以绕过命令限制了
* 红石限制模块增加至2种可选的检测器，BLOCK_COUNTER 可用于仅检测高频红石，不检测低频红石
* Bug 修复
* ...

# 2022/01/12 v3.0.1-beta

* 修复权限判断异常的 bug

# 2022/01/13 v3.0.2-beta

* 修复 BossBar 通知读取异常的 bug

# 2022/01/13 v3.0.3-beta

* 区块运行时间红石检查器更名

# 2022/01/14 v3.0.4-beta

* 修复调度器无任务时报错的问题
* 修复调度器在重载后发生异常的问题