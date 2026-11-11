# 0.1.1

## 中文

- **适配 DSH 0.2.2-rc.1**（0.2.1-rc.5 / 0.2.1-rc.2 仍兼容）。0.2.2 把大量代码搬到了新文件，旧补丁按“路径 + 逐字节 pattern”匹配必然全灭，状态页会把这种情况误报成“官方改了我的原文”（drifted）。
- **十条补丁重新对齐**：#14（`displayReason` 抽成 `escalationDisplayReason()`）、#17（`editIntent` 抽成 `requirePresent()`）、#40（`assemble` 早退分支多返回 `tools`）、#54（文案搬到 `dsh-deliverable-file-references`）、#60（迁到 `dsh-tool-executor-ptc`）、#62（流水线拆到 `lib/pipeline.js`）、#67（缩进 4 tab → 5 tab）、#74（`claim` 与 `assemble` 之间插入了 model/toolSet 解析）、#76（官方身份句已不存在）、#82（后继 region 从 `home-paths` 变成 `mime`）。旧落点的 pattern 全部保留。
- **新增遗留位置（legacyKeys）概念**：代码搬走后旧文件不再命中时按 skipped 收口，不再刷成 drifted。判定只按精确包目录名，`dsh-tools` 不会误伤 `dsh-tool-executor-ptc`。
- **新增 `textMayBeAbsent`**：目标文本在上游本来就可能不存在的补丁（#76）不再被误判为漂移。
- **修复红队预设加载失败**：`dsh-purge-community/redteam/tools` 是过时包名，`@deepseek-ai/dsh-tool-subagent-control/list-agents` 是不存在的子路径。两者都会让整份 preset 判为 broken、模式下拉里选不中。`renderToolsPackage()` 现在能识别两代历史包名；`repairDelegation()` 会删掉残留的失效子路径行，用户磁盘上的老预设下次启动自动修复。
- **修复 #92 / #93 的重复插入**：这两条补丁的 `replace` 以 `pattern` 原文开头（锚点保留型），却没有幂等挡板，**每 apply 一次就往 `app/lib/main.js` 里再插一份**。实测该文件已累积 13 份退出追踪块和 13 个每 60s 写日志的定时器（838 KB → 870 KB）。现在加了 `skipIfMarked` 与 `dedupeInsert`，并把已重复的块折叠回一份。

## English

- **Targets DSH 0.2.2-rc.1** (0.2.1-rc.5 / 0.2.1-rc.2 still compatible). 0.2.2 moved a lot of code to new files; patches matched by "path + byte-exact pattern" all missed, and the status page misreported that as upstream rewriting our text ("drifted").
- **Ten patches realigned**: #14 (`displayReason` extracted into `escalationDisplayReason()`), #17 (`editIntent` extracted into `requirePresent()`), #40 (`assemble` early return now also returns `tools`), #54 (text moved to `dsh-deliverable-file-references`), #60 (moved to `dsh-tool-executor-ptc`), #62 (pipeline split into `lib/pipeline.js`), #67 (indent 4 tab → 5 tab), #74 (model/toolSet resolution inserted between `claim` and `assemble`), #76 (the stock identity sentence no longer exists), #82 (following region changed from `home-paths` to `mime`). Every old-location pattern is kept.
- **New legacyKeys concept**: when code has moved away, a miss in the old file settles as skipped instead of drifted. Matching uses the exact package directory name, so `dsh-tools` no longer catches `dsh-tool-executor-ptc`.
- **New `textMayBeAbsent`**: patches whose target text may legitimately be absent upstream (#76) are no longer reported as drift.
- **Fixed red-team preset failing to load**: `dsh-purge-community/redteam/tools` is a stale package name, and `@deepseek-ai/dsh-tool-subagent-control/list-agents` is a subpath that does not exist. Either one marks the whole preset broken and hides it from the mode dropdown. `renderToolsPackage()` now recognizes both historical package names, and `repairDelegation()` drops leftover dangling-subpath rows, so presets already on disk heal on the next start.
- **Fixed duplicate insertion in #92 / #93**: both patches have a `replace` that starts with its own `pattern` (anchor-retaining) but had no idempotency guard, so **every apply appended another copy into `app/lib/main.js`**. On the test machine that file had accumulated 13 exit-trace blocks and 13 log-flushing timers running every 60s (838 KB → 870 KB). Both now carry `skipIfMarked` and `dedupeInsert`, and already-duplicated blocks collapse back to one.
