# Fable5 复盘笔记 — skills

> 复盘日期: 2026-07-07 · Reviewed by Claude (Fable 5)

这是你的个人 Agent Skills 库，采用"一处编写、两处使用"的架构：`bin/sync` 把技能软链到 `~/.claude/skills/` 供 Claude Code 全局使用，`bin/package` 打包 zip 手动上传 claude.ai。目前只有 2 个上线技能（`personal/grill-me` 自写、`personal/teach` 从 mattpocock/skills 引入并带 SOURCE.md 出处说明），`engineering/` 和 `wip/` 完全为空。仓库共 3 次提交（6/8 建仓、6/23 加 teach），距今已静默两周——基础设施搭得很好，但内容填充明显滞后。

## 1. 哪些任务可以做成 Skill

**现有清单：**
- `personal/grill-me/` — 自我拷问式访谈，已同步且可用，状态完整
- `personal/teach/` — 多会话教学工作区（含 5 个格式文件），vendored，状态完整
- `engineering/` — **空**（只有 .gitkeep）
- `wip/` — **空**，没有烂尾草稿（好事，但也说明没有在孵化新技能）

**注意一个重复：** `personal/grill-me` 与已安装的 `anthropic-skills:grill-me` 插件描述完全相同，两边同时存在会产生双触发条目。建议二选一：留个人库版本（可自由改），卸载/忽略插件版。

**结合其他项目的新技能候选（填 engineering/ 的空）：**
- `engineering/vendor-skill` — 你 6/23 手动完成过一次"从 GitHub URL 引入第三方技能 + 写 SOURCE.md 出处 + commit"的完整流程。这个流程完全可以固化成技能或 `bin/vendor` 脚本。
- `engineering/court-monster-triage` — court-monster 是你用得最重的项目（订场自动化），失败排查/日志检查/预订状态确认这类重复操作值得做成项目级技能（放 `court-monster/.claude/skills/`，用 `CLAUDE_SKILLS_DIR` 指过去，README 里你自己写了这个用法但从没用过）。
- `engineering/supabase-checklist` — gap_days 是 Next.js+Supabase，migration/RLS/部署前检查是典型可技能化流程。
- `engineering/news-digest-review` — ai-news-aggregator 的产出质量抽查/prompt 调优流程。
- rallynote 还在想法阶段，正适合先在 `wip/` 里放一个 `wip/rallynote-spec` 草稿技能练手。

## 2. 哪些流程可以自动化

**bin/ 现状（质量很高，shell 写得规范）：**
- `bin/new` — 从模板脚手架，校验分类和命名
- `bin/sync` — 软链 + 清理失效链接 + 防覆盖真实目录（不会碰 gstack 的技能目录）
- `bin/package` — 打 zip 到 dist/，排除 .DS_Store

**可补的自动化：**
1. **`bin/validate`** — sync 前 lint frontmatter：name 与文件夹名一致、description ≤1024 字符、必填字段存在。现在写错了要到 Claude Code 触发失败才发现。
2. **claude.ai 上传半自动化** — 你 6/8 尝试过全自动上传后放弃（"算了我手动上传吧"）。claude.ai 确实没有上传 API，但现在你有 claude-in-chrome 浏览器 MCP，可以做一个 `engineering/upload-skill` 技能：package 后用浏览器自动走 Settings → Capabilities → Skills → Upload。这是当时放弃的痛点，现在有解了。
3. **git hook 自动 sync** — post-commit/post-merge 里跑 `bin/sync`，省掉"新增技能后忘记 sync"这一步。
4. **`bin/package --all`** — 一次性重打所有技能的 zip；目前 teach 从未打包过。

## 3. 哪些提示词一直在重复

提示词历史只有 8 条，但模式清晰：
- **"commit" / "commit and push" 出现 3 次**（8 条里占近一半）。这在你所有项目里应该都是高频尾句——要么用 gstack 的 `/ship`，要么在 CLAUDE.md 里约定"完成改动后主动提议 commit"，省掉这句。
- **"把 readme 写清楚 + stage commit push"** — 建仓型项目的标准收尾组合，gstack 已有 `/document-generate` 覆盖。
- **自动化尝试后回退**（"直接自动化给我实现" → "算了我手动上传吧"）— 说明遇到平台限制时值得先花 2 分钟确认可行性再动手；这条痛点见上文第 2 节的浏览器方案。

## 4. 一次性代码 / 清理建议

这个仓库非常干净，没有烂尾 wip、没有死代码。仅有轻量清理项：
- `personal/.gitkeep` — `personal/` 下已有两个真实技能，占位文件失去意义 → **本 PR 已移除**（`engineering/.gitkeep`、`wip/.gitkeep` 保留，那两个目录还是空的）。
- `dist/grill-me.zip` — 已 gitignore、随时可用 `bin/package` 重新生成，上传早已完成，本地可手动删。
- `.DS_Store` ×3（根目录、`personal/`、`templates/`）— macOS 垃圾，已 gitignore，本地可手动删。
- **不需要清理但值得注意：** teach 技能是"unmodified copy"，上游 mattpocock/skills 若更新你不会知道。SOURCE.md 里记了日期，半年后可以对一次 diff。

## 5. 常规使用方法 & 经验总结

**做对了的事（值得在其他项目复制）：**
- 软链而非拷贝：改技能内容即时生效，只有增删改名才需要重跑 sync——这个设计消灭了"改了没生效"的整类问题。
- `bin/sync` 的防覆盖逻辑：只管理自己创建的软链，遇到真实目录（gstack 技能）跳过并警告。防御性脚本的范本。
- vendored 技能带 `SOURCE.md`（来源 + 日期 + 许可证），出处可追溯。
- `wip/` 不同步的约定：草稿不会意外污染全局技能列表。

**待改进：**
- **仓库利用率低是最大问题**：两周零提交、`engineering/` 空置，而你日常 Claude Code 时间大头在 court-monster 等编码项目上。建议建立一个习惯回路：每次项目复盘（/retro 或 /learn）发现可重复流程 → `bin/new wip xxx` 落草稿 → 打磨后移入正式目录。技能库的价值随技能数量复利增长，现在的 2 个还没到临界点。
- README 里写好的 `CLAUDE_SKILLS_DIR` 项目级用法从未真正使用——court-monster 是第一个该用它的项目。
- 与 gstack/anthropic-skills 插件的边界要想清楚：插件覆盖的（ship/qa/retro）不要重造，个人库专注"你独有的流程"（订场排查、新闻管线、vendor 流程）。grill-me 的重复就是边界模糊的信号。
