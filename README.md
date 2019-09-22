# HoshinoPlan：clanbattle

Nonebot会战管理插件开发计划

## 简介

基于nonebot的QQ机器人框架，开发插件`clanbattle`用于群内会战管理。

## 快速指南 - QuickStart

**Q：这个机器人是干什么的？要怎么用？**

**A：**本机器人旨在帮助公会记录会战出刀情况，并支持分数统计、一键催刀等功能。邀请机器人帐号进入群后，输入命令即可调用相应功能，详见下节[支持命令一览]。

**Q：命令是啥？能吃吗？**

**A：**命令是指机器人可以识别的一系列指令，可以指挥机器人做你希望做的事情。但机器人很笨，所以在你给出指令时请务必注意**空格**、**短横线**（减号）等符号，否则将无法成功执行。

**Q：我就是个玩母猪链接的，记不住那么多，教我怎么报刀就行了**

**A：**首次使用机器人时，请输入`join-clan`加入到本群公会（如需加入2会，请输入`join-clan --cid 2`）；之后，您只需要使用`dmge`命令进行简易报刀就可以了，格式为`dmge 伤害数字 r周目 b老几 [last|ext|timeout]`，具体例子见下：

- 骑士A对5周目老4造成了1919810点伤害

    ```
    dmge 1919810 r5 b4
    ```

- 骑士B对6周目老1造成了114514点伤害收了尾刀

    ```
    dmge 114514 r6 b1 last
    ```

- 骑士B又用补偿时间对6周目老2造成了1919点伤害

    ```
    dmge 1919 r6 b2 ext
    ```

- 骑士C打11周目老1时，伊莉雅连续暴毙两次挂树上，但挂的时间太长掉线了，无奈掉刀

    ```
    dmge r11 b1 timeout
    ```

如果您觉得dmge不好输入，也可使用汉字`刀`来替代。相信以上例子已经覆盖了绝大多数普通成员使用场景。

**Q：我手滑报刀报错了，怎么办？**

**A：**目前请联系管理员在后台进行更改，改刀命令正在开发中...

**Q：除了报刀还有啥功能？**

**A：**查看本日余刀`show-remain`（管理员使用时将进行一键催刀）、本月会战分数统计`stat`等，boss订阅、出刀排队等功能正在开发中，更多功能请查阅[支持命令一览]。另外还有常用链接速查、精致睡眠套餐、在线翻译、~~涩图~~等彩蛋功能。

**Q：我是会长，我们一个群里有99个分会，有群友手里5个小号打会战，机器人报刀不会乱吗？**

**A：**开发时已考虑过这种场景，您可以使用`--cid`参数来指定分会编号（不指定时默认为1，即1会），成员可以使用`--alt`参数指定小号的编号，具体使用请参考每条命令的帮助，详见[支持命令一览]。

**Q：要是有人漏报、没有及时报、报伤害报多了，数据会不会混乱？**

**A：**由于机器人无法直接获取游戏内数据，设计原则上只能信任用户提供的数据。但开发时已考虑到上述情况并进行了相应处理，数据异常时会自动修正或进行提醒，报刀顺序并不会影响当前会战进度的计算。不过，由于本机器人仍在开发中，确实有可能出现意料之外的情况，如有bug，欢迎联系开发组并提供复现方法。

**Q：报刀太麻烦了，能不能做个直接获取游戏内数据的机器人出来？**

**A：**理论上是可以的，只需要将机器人帐号拉入公会（29人打会战），定时刷新会战情况，抓下数据分析就能够实现。限于开发成本与技术难度，目前不计划实现，如果您有移动端网络抓包、数据解密等方面的经验，欢迎与开发组联系。

**Q：我们就是个休闲公会，没必要整这么麻烦**

**A：**~~机器人本来也不是给咸鱼休闲公会开发的呢亲~！~~



## 应用背景及需求

本插件运行于nonebot之上，nonebot与酷Q通过CQHttp通信，完成QQ消息的接受与发送。

本插件应支持：

- [x] 单机器人账号管理多个公会
- [x] 多个公会共用一个QQ群：分为一会/二会/三会/...
- [x] 单个QQ号可能拥有多个游戏账号、分属不同公会
- [ ] 对公会使用进行授权
- [ ] 管理公会的增查删改
    - 权限：群主：可修改、删除、查询本公会但不能添加新公会
- [ ] 公会人员的增查删改
    - 权限：群主：均可；本人：仅可对自己进行
- [x] 报刀报伤害
- [ ] 出刀排队/预约boss
- [ ] 新boss提醒



## 支持命令一览

*SUPERUSER不受权限控制影响*

### 公会管理

| 进度 | 命令                 | 参数              | 权限                    | 说明                                                         |
|---| -------------------- | ----------------- | ----------------------- | ------------------------------------------------------------ |
| ok | add-clan             | [--cid] [--name]  | GROUP_OWNER/GROUP_ADMIN | 添加新公会，编号为id，默认为该群最大公会id+1，若无公会则为1；name为公会名 |
| ok | list-clan            |            | GROUP_MEMBER            | 默认显示当前QQ群的所有公会；--all 显示管理的所有公会，仅SUPERUSER可用 |
|  | mod-clan             | --cid  --new_name | GROUP_OWNER/GROUP_ADMIN | 修改公会的name                                               |
| | del-clan             | --cid             | GROUP_OWNER/GROUP_ADMIN | 删除公会                                                     |

### 成员管理

|进度| 命令                 | 参数                            | 权限            | 说明 |
|--| -------------------- | ------------------------------- | --------------- | ---- |
|ok| add-member/join-clan | [--cid] [--uid] [--alt] [--name] | SUPERUSER       | 将[uid]的小号[alt]加入[cid]会，游戏内ID为[name]。参数缺省时将会将命令发送者的大号加入1会，自动获取群名片作为name。 |
|ok| list-member          | [--cid]                         | SUPERUSER/OWNER | 列出[cid]会的成员。默认为1会 |
|| mod-member           | [--clan] [--uid] [--alt] --name | SUPERUSER/OWNER |      |
|| del-member           | [--clan]                        | SUPERUSER/OWNER |      |

### Boss信息查询



### 出刀管理

|进度| 命令                | 参数                                  | 权限         | 说明                                                         |
|--| ------------------- | ------------------------------------- | ------------ | ------------------------------------------------------------ |
|| subscribe           |                                       |              | 预约boss，到达时提醒                                         |
|ok| add-challenge / dmg | --round --boss damage [--uid] [--alt] | GROUP_MEMBER | 报刀。[uid]的帐号[alt]对[round]周目第[boss]个Boss造成了[damage]点伤害。 |
|ok| dmge / 刀           | r? b? 伤害数字                        | GROUP_MEMBER | 简易报刀。例，对5周目老4造成了1919810点伤害：dmge 1919810 r5 b4      |
|| mod-dmg             |                                       |              | 改刀                                                         |

### 会战统计

| 命令        | 参数              | 权限         | 说明                                       |
| ----------- | ----------------- | ------------ | ------------------------------------------ |
| show-remain | [--cid]           | GROUP_MEMBER | 查看今日余刀，管理员调用时将进行一键催刀。 |
| stat        | [--cid]           | GROUP_MEMBER | 当月会战分数统计，按分数排名               |
| plot        | [--today] [--all] |              | 绘制会战分数报表                           |





## 数据储存

### 术语

- gid：group id QQ群号
- cid：clan id 群内下属分会编号

- uid：user id 用户的QQ号
- alt：用户的小号编号（默认为0，主账号）
- ~~clan：公会标识，由gid与cid组成`[gid]_[cid]`~~

```
clanbattle.db
├─clan
├─member
├─subscribe
├─battle_[gid]_[cid]_[yyyymm]

```

### config.py

配置boss血量信息、提供简易版作业等

### clan.csv

公会以gid+cid作为唯一标识，name可修改可重复

- gid：qq群号
- cid：该群下的公会号
- name：公会名



### ~~progress.csv~~

~~记录各个公会进度~~ 不再需要，可以根据出刀情况计算

- gid
- cid
- round
- boss

### member.csv

记录成员信息

- uid：qq号
- alt：小号编号
- name：游戏内名字
- gid：所属公会群
- cid：所属公会分会

### subscribe.csv

订阅信息

- sid：订阅编号

- uid：qq号
- alt：小号编号
- gid
- cid
- boss：订阅boss

### battle/[gid]\_[cid]\_[yyyymm].csv

记录出刀信息

- eid：出刀记录编号
- uid：出刀者qq号
- alt：出刀者小号编号
- time：出刀时间`ddhhMMss`
- round：周目数
- boss：Boss编号
- dmg：伤害
- flag：出刀标志 normal/last/extend



## 开发思路

### 一次报刀发生了什么？

首先，获取到dmg命令，提取其中的round、boss、damage/score信息，同时获取报刀者的uid（若给出了alt，则确认其小号）；

然后，根据uid和alt查member表，查出其所属公会的gid和cid；

之后，查询对应的battle记录是否在内存中（若无则载入），录入出刀信息，写入文件；

最后，更新该公会的进度，触发订阅信息。

### 一次统计报表发生了什么？

首先，获取到plot命令，得知gid和cid；

查询对应时间段的battle记录，统计每个账号的出刀记录；

汇总报表（并绘图），输出；

### 分层设计

- 命令层 - `__init__.py`中的各种命令：接受机器人指令并调用服务层处理
- 服务层 - BattleMaster：将数据层的基本操作组合，形成服务
- 数据层 - DAOs：直接与SQLite交互，支持基本的增查删改



## 心得

- 学习了SQLite的使用（增查删改/CRUD）
- 实践了一下DAO的设计模式；但由于本项目并没有那么庞大，而且Python语言十分便利，并没有完全按照Java的面向接口开发模式来操作，不过以后如果需要改用别的数据库，只需要增加一个dao/xxxdao.py，修改battlemaster的import语句即可。

- 学习了python的命令行参数处理工具 [Argparse](https://docs.python.org/zh-cn/3.6/howto/argparse.html) 