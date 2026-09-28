# Helldivers2 SteamID-Tracker

# kick — SteamID 追踪 & 黑名单检测

**版本：** kickv19b
**依赖：** Bingus Shared Loader（loader v15 以上 / API 1）
**热键：** `]`
**类型：** 客户端本地 mod，只影响你自己的屏幕
可随意解包更改优化进一步做出更优秀mod，后续应该也不会有更新了，也无法为我未预料到的bug而作更新
---

## 这个 mod 干什么

进任务后按一次 `]`，它会扫描当前战局里所有玩家的 SteamID，和你维护的黑名单做比对。

- **命中黑名单**：屏幕中央弹出红色警告，显示命中了几个人、来自哪个黑名单文件、以及对方的 Steam 主页链接。
- **没命中**：什么都不显示，但会把扫描时本局所有玩家的 SteamID 写到文件里，方便你事后查看。

**它不会踢人、不会自动操作、不会修改游戏行为。** 只是个"识别器"。

---

| 步骤 | 操作 |
|---|---|
| 1 | 飞船上＋任务里有人就可以扫，不排除你自己（重要，扫描只认当前在局的玩家） |
| 2 | 按 `]` |
| 3 | 等 **8~15 秒**（全内存扫描，中途别按第二次，也别退任务，这几把AI说的，自己体会需要时间即可，反正别连按） |
| 4 | 如果命中，屏幕中央会弹 4 秒的红色警告 |（这个时候懒得看就F12截图，也就给客机用的扫的时候撞上了提个醒）

**扫描期间游戏可以正常玩，但会有一点点卡顿**（每秒几毫秒的开销，我上大学时候的旧笔电几乎没感觉），扫完就恢复。

---

## 屏幕上看到什么

命中时，屏幕中央从上到下显示：

```
[!] BLACKLIST: 1
source kick_blacklist.txt:24
https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX
show 4s, F12 screenshot
```

| 行 | 含义 |
|---|---|
| `[!] BLACKLIST: N` | 本局命中 N 个人 |
| `source 文件名:行号` | 是在黑名单文件的第几行匹配到的 |
| URL | 对方的 Steam 主页，**点开就能看到是谁** |
| `show 4s, F12 screenshot` | 提示：4 秒后消失，想留证按 F12 |

**`F12` 是 Steam 自带的截图键。** 注意：截图会**连同这个警告框一起拍进去**——这正是我们要的效果，方便你事后举证。

4 秒没截图就没了，但 `blacklist_hits.txt` 会永久记录，见下文。

---

## 黑名单怎么维护

所有文件在：

```
%LOCALAPPDATA%\CowboyBingus\Helldivers2\Logs\
```

复制上面这行，粘贴到资源管理器地址栏，回车即达。

### 两个黑名单文件

| 文件 | 用途 |
|---|---|
| `kick_blacklist.txt` | **你自己维护的**黑名单 |
| `kick_blacklist_imported.txt` | **别人分享给你的**黑名单（分开写便于区分来源） |

**两个文件的格式完全一样**，命中时日志会记录是在哪个文件哪一行匹配的。

### 每行格式（二选一）

**格式 A：完整三列**（从本 mod 生成的 `player_*.txt` 直接复制，最省事）

```
39XXXXXXXXXXXXXX  01XXXXXXXXXXXXXX  https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX
```

**格式 B：只写 Steam 主页**

```
https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXXX
```

或者干脆只写 **17 位数字**：

```
76XXXXXXXXXXXXXXX
```

两种都会自动归一化，**写哪种都行**。

### 规则

- `#` 后面是注释，可以写劣迹说明，比如：
  ```
  39XXXXXXXXXXXXXX  01XXXXXXXXXXXXXX  https://steamcommunity.com/profiles/76XXXXXXXXXXXXXXX  # 2026-09-15 疑似自瞄
  ```
- 空行忽略
- **大小写不敏感**（统一按大写比对）
- 三个 ID（peer_id / steam64_hex / steam64_url）**任一命中即算命中**，写错的列不会拖垮其它列
- 格式错误的行会被记到 `blacklist_hits.txt` 里（`# error @ ...`），方便你排查

### 修改后需要重启游戏吗？

**不需要。** 每次按 `]` 扫描时都会重新读取两个黑名单文件。改完保存即可，下次扫描生效。

---

## 每次扫描会产生的文件

| 文件 | 内容 |
|---|---|
| `player_MMDD_HHMMSS.txt` | 本局所有玩家的三列信息（peer_id / steam64 / URL） |
| `blacklist_hits.txt` | **追加模式**，所有历史命中记录，不会覆盖 |
| `Kick.log` | 技术日志，排查问题用，平时不用看 |

**`player_*.txt` 怎么用**：打开它，找到有问题的玩家那一行，**整行复制**粘贴到 `kick_blacklist.txt`，行尾加 `# 加你的备注`，保存。下次遇到他就报警了。

反正除了kick_blacklist.txt，kick_blacklist_imported.txt，你按一次会产生一个对于对应时间戳的play文本，没事可以删，用的多也就多，然后kick_blacklist.txt，kick_blacklist_imported.txt
你用过mod之后删了，还会不会再给你像第一次用自动生成默认文件，这个我不知道，刚想起来才想起来，懒得再测了，没有就自己新建一个同名即可。

## 常见问题


**Q：扫描很久？**
A：正常 8~15 秒。这是全内存扫描的代价，扫完自动停。

**Q：HUD 显示 4 秒太短，来不及看？**
A：不影响记录。所有命中都写进了 `blacklist_hits.txt`，游戏结束后慢慢看。

**Q：`7656119` 开头的 URL 打不开 / 404？**
A：多半是从别处复制过来的 steam64 数字**少了或多了一位**。对照 `player_*.txt` 里的原始 URL 核对。

**Q：会影响到队友吗？**
A：**不会。** 这是纯本地 mod，只在你自己的客户端跑。别的玩家看不到你的黑名单、看不到你的警告框、也感知不到你在扫描。

**Q：会被反作弊封号吗？**
A：已经大mod时代了，我搓这个mod纯为迎合时代罢了，这个 mod **不读游戏进程外的内存、不修改游戏代码段、不写任何游戏内存**，只在游戏内读自己进程里的内存做字符串匹配，并且写入的只有本地日志文件。是否使用由你自行判断并承担风险。

**Q：能自动踢人吗？**
A：不能。这是识别工具，踢人还是得你自己动手。

---

## 卸载

在 HD2 Mod Manager 里禁用或删除 `kick`，然后 Deploy。所有产生的日志文件不会自动清除，你可以手动删掉 `%LOCALAPPDATA%\CowboyBingus\Helldivers2\Logs\` 下你不需要的文件。

---

## 致谢 & 免责
感谢各方开源和些mod给我的经验，感谢DS
本 mod 用于**自我保护**：识别、记录并分享已知的问题玩家。请勿用于骚扰、报复或任何违反 Steam 服务条款的行为。所有数据仅存于本地，是否分享由你决定。
