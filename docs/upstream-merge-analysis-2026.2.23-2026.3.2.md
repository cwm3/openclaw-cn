# 上游合并分析: v2026.2.23 → v2026.3.2

> 生成日期: 2026-03-16
> 上游项目: openclaw/openclaw
> 目标分支: openclaw-cn (中国本地化分支)
> 关联 Issue: #479
> 用户约束: 飞书保留本地实现，跳过海外渠道（Telegram/Discord/Slack等），仅合并安全+核心更新

## 概览统计

**上游范围**: v2026.2.23 → v2026.3.2 (2,177 commits)
**本地偏差**: 699 个本地提交 (本地化/重品牌/飞书/中文CLI)
**Merge base**: `e6bdffe568`

### 提交分类总览

| 操作     | 数量      | 说明                                       |
| -------- | --------- | ------------------------------------------ |
| MERGE    | 374       | 需要合并的核心/安全/关键修复               |
| OPTIONAL | 981       | 可选（重构/优化/锦上添花）                 |
| REVIEW   | 85        | 需要人工审核（均为飞书渠道修复）           |
| SKIP     | 736       | 跳过（CI/CD/文档/测试/未使用渠道/原生App） |
| **合计** | **2,176** |                                            |

### MERGE 细分

| 类别         | 数量 | 说明                            |
| ------------ | ---- | ------------------------------- |
| CRITICAL-BUG | 185  | 关键 Bug 修复（全部为核心代码） |
| SECURITY     | 139  | 安全修复                        |
| CHANNEL-FIX  | 50   | 渠道特定修复                    |

### SKIP 细分

| 类别           | 数量 |
| -------------- | ---- |
| TEST-ONLY      | 193  |
| NATIVE-APP     | 169  |
| UNUSED-CHANNEL | 126  |
| CHORE          | 123  |
| DOCS           | 91   |
| CI-CD          | 32   |
| DOC-ONLY       | 2    |

---

## 按用户约束过滤后的合并计划

### 过滤规则

- ✅ **合并**: 核心代码 CRITICAL-BUG + SECURITY + 通用 CHANNEL-FIX
- ❌ **跳过**: 海外渠道（Telegram/Discord/Slack/Signal/Line/Matrix/Zalo/IRC/MSTeams/Nostr/Tlon/Nextcloud/Twitch/BlueBubbles）
- ❌ **跳过（保留本地）**: 飞书（extensions/feishu/, src/feishu/ 保持当前本地版本）

### 过滤后的合并数量

| 类别         | 需合并          | 跳过（海外渠道） |
| ------------ | --------------- | ---------------- |
| CRITICAL-BUG | 185             | 0                |
| SECURITY     | 115 → **120** ¹ | 24 → **19** ¹    |
| CHANNEL-FIX  | 12              | 38               |
| **合计**     | **~317**        | **~57**          |

¹ 在24个标记为跳过的安全提交中，有5个实际修改了共享基础设施代码，需要合并（见下方详情）。

### 需合并的 CHANNEL-FIX（12个，均为本地使用的功能）

| SHA            | 描述                                                   |
| -------------- | ------------------------------------------------------ |
| `0743463b880f` | fix(webchat): 修复聊天记录渲染中的 NO_REPLY token      |
| `8e48f7e35361` | fix(tui): 为 URL 覆盖尊重显式 gateway auth             |
| `5c1eb071ca77` | fix(whatsapp): 恢复中继代理的直接入站元数据            |
| `e1bf9591c337` | fix(web-tools): 允许 RFC2544 可信 fetch 范围           |
| `1a42ea3abfa2` | fix(auto-reply): 规范化 block-reply 回调的 Promise     |
| `a6a742f3d067` | fix(auto-reply): 来自 @scoootscooob 的修复             |
| `d6cbaea434d8` | fix(tui): 保持工具调用过渡期间的流式文本               |
| `d8e2030d478d` | fix(web-search): 尊重 HTTP_PROXY 环境变量              |
| `e64d72299e06` | fix(auto-reply): 收紧静默 token 语义和前缀流           |
| `177386ed7318` | fix(tui): 修复 session 中无 modelProvider 时的前缀错误 |
| `e22a2d77baf3` | fix(whatsapp): 修复不可重试 440 关闭时的重试循环       |
| `13a1c4639678` | fix(web-search): 减少 provider 自动检测日志噪点        |

### 涉及共享基础设施的安全提交（从"跳过"中恢复为"合并"）

这5个提交虽然在提交消息中提及了海外渠道，但实际修改了共享代码文件：

| SHA            | 描述                                            | 共享文件                                 |
| -------------- | ----------------------------------------------- | ---------------------------------------- |
| `1a0036283d81` | refactor(security): dedupe allowlist validation | `src/security/audit-channel.ts`          |
| `5a32a66aa8ac` | perf(core): 加速路由/配对/安全扫描              | `src/routing/`, `src/security/`          |
| `a9f118878542` | sessions_spawn: 内联附件+编辑+生命周期          | `src/config/zod-schema.agent-runtime.ts` |
| `17bae93680f0` | fix(security): 通配符 origins 警告              | `src/security/audit.ts`                  |
| `dd9ba974d035` | fix: SSRF pinned DNS IPv4 排序优先              | `src/infra/net/ssrf.ts`                  |

### 安全提交中真正可安全跳过的（19个，仅修改渠道隔离文件）

这些提交仅修改各自渠道目录下的文件，不影响共享代码：

- Telegram: 3, Slack: 3, MSTeams: 3, Nextcloud: 3, Zalo: 2, Line: 1, Feishu: 2 (保留本地), BlueBubbles: 1, IRC: 1, Tlon: 1, Docs-only: 2

---

## 冲突分析

### 总体冲突预估

使用 `git merge-tree` 模拟合并结果：

| 指标         | 数量                          |
| ------------ | ----------------------------- |
| 总冲突文件数 | **1,594**                     |
| 海外渠道冲突 | 273（可自动用 `--ours` 解决） |
| 飞书冲突     | 33（可自动用 `--ours` 解决）  |
| **核心冲突** | **1,288**                     |

### 核心冲突类型

| 类型          | 数量  | 说明                           |
| ------------- | ----- | ------------------------------ |
| content       | 1,020 | 内容冲突（大多为本地化重命名） |
| add/add       | 160   | 双方新增同名文件               |
| modify/delete | 112   | 一方修改、另一方删除           |
| other         | 10    | 重命名冲突等                   |

### 核心冲突目录分布（前15）

| 目录             | 冲突数 |
| ---------------- | ------ |
| `src/agents`     | 177    |
| `src/commands`   | 109    |
| `src/gateway`    | 79     |
| `src/cli`        | 77     |
| `src/infra`      | 70     |
| `src/auto-reply` | 68     |
| `src/config`     | 57     |
| `ui`             | 51     |
| `src/browser`    | 36     |
| `src/cron`       | 30     |
| `apps/macos`     | 29     |
| `docs/gateway`   | 20     |
| `docs/platforms` | 20     |
| `docs/cli`       | 19     |
| `src/hooks`      | 19     |

### 增量合并可行性

| 中间 Tag   | 提交数 | 冲突数 |
| ---------- | ------ | ------ |
| v2026.2.24 | 228    | 1,549  |
| v2026.2.25 | 387    | 1,551  |
| v2026.2.26 | 727    | 1,562  |
| v2026.3.1  | 1,315  | 1,573  |
| v2026.3.2  | 2,177  | 1,594  |

> ⚠️ 结论: 冲突数在各标签间几乎没有显著差异（1,549 → 1,594），说明绝大多数冲突来自本地699个提交的已有分歧，而非上游新增。增量合并无法减少冲突量，建议直接合并到 v2026.3.2。

---

## 推荐策略: 全量合并 + 选择性还原

### 策略说明

直接 `git merge v2026.3.2`，然后对海外渠道和飞书目录执行 `git checkout HEAD --`（保留本地版本），再手动/半自动解决核心冲突。

### 执行步骤

```bash
# 1. 创建集成分支
git checkout -b merge/v2026.3.2 main

# 2. 执行合并（不自动提交）
git merge v2026.3.2 --no-commit --no-ff

# 3. 海外渠道目录 → 保留本地版本（自动解决 273 个冲突）
git checkout HEAD -- \
  extensions/telegram/ extensions/discord/ extensions/slack/ \
  extensions/signal/ extensions/line/ extensions/matrix/ \
  extensions/zalo/ extensions/zalouser/ extensions/irc/ \
  extensions/msteams/ extensions/nostr/ extensions/tlon/ \
  extensions/nextcloud-talk/ extensions/twitch/ extensions/bluebubbles/ \
  src/telegram/ src/discord/ src/slack/ src/signal/ src/line/

# 4. 飞书 → 保留本地实现（自动解决 33 个冲突）
git checkout HEAD -- extensions/feishu/ src/feishu/

# 5. 处理剩余 ~1,288 个核心冲突
#    - 大量为本地化重命名冲突，可用 sed 半自动处理
#    - 典型模式: openclaw → openclaw-cn, Openclaw → OpenClaw-CN 等
#    - 预计 ~200-300 个需要人工逐一审查

# 6. 验证
pnpm install
pnpm lint
pnpm build
pnpm test

# 7. 提交并合回 main
git commit -m "merge: upstream v2026.3.2 (security+core, skip overseas channels)"
git checkout main
git merge merge/v2026.3.2
git branch -d merge/v2026.3.2
```

### 工作量预估

| 部分                         | 预计情况                                        |
| ---------------------------- | ----------------------------------------------- |
| 海外渠道+飞书（306个冲突）   | 自动解决                                        |
| 核心 content 冲突（1,020个） | ~70% 可用 sed 半自动（本地化模式），~30% 需人工 |
| add/add 冲突（160个）        | 多为新增文件，逐一决定保留哪方                  |
| modify/delete（112个）       | 逐一决定是否保留                                |
| 构建+测试修复                | 合并后必须通过 lint/build/test                  |

### 风险与注意事项

1. **共享安全代码**: 5个涉及 `src/security/`、`src/routing/`、`src/infra/` 的安全提交来自海外渠道消息，但修改的是共享代码——这些**必须**包含在合并中
2. **飞书安全**: 6个飞书安全提交（`bbab94c1fead` 等）修改了 `extensions/feishu/` 下的文件，保留本地版本意味着需要手动验证本地实现是否已覆盖这些安全修复
3. **OPTIONAL 981个提交**: 多为重构和优化，本次不合并但后续可选择性 cherry-pick
4. **REVIEW 85个提交**: 全部为飞书渠道修复，因保留本地实现而跳过

---

## 附录: OPTIONAL 提交分类

| 类别              | 数量 |
| ----------------- | ---- |
| UNKNOWN（未分类） | 743  |
| REFACTOR          | 192  |
| NICE-TO-HAVE      | 46   |
