# oss-pr-flow

## 这是什么

一个给 AI 编码助手用的**开源 PR 贡献全流程** skill。单文件（`SKILL.md`），无依赖。

从"找一个值得做的点"到"提交一个经得起 review 的 draft PR"，分成五道闸门，每道闸门不满足通过条件就停下，不带病进入下一步。

## 流程

**选题 → 修复 → 成稿 → 审查 → 提交**

- **选题**：三个来源（现有 issue / 读源码 / 深度使用）并行侦察，允许零产出，找不到就如实结束，不硬凑。候选去重查重后按"改动可控、证据充分、维护者接受概率"排序；动手前做一次撞车检查（无人认领、无关联 open PR），然后直接开工，不等确认。
- **修复**：先复现出失败再改代码；回归测试的合格标准是"撤掉修复后必须失败"；区分"修复验证"与"护栏用例"，不笼统写"新增 N 个用例全部通过"；diff 不夹带格式化等无关改动。
- **成稿**：按仓库自己的模板章节起草，没有模板才用最小集；只写有证据的内容。
- **审查**：忘掉作者身份，重新从 diff 和测试输出取证。每句断言按证据分级（E1 直接证据 / E2 间接证据 / E3 推断 / E0 无据）；**降级前必须先搜过**；改动涉及错误码/默认值时必查下游消费方；必查改动路径当前是否可达（被 feature flag 关掉就必须主动说明）。产出终稿 + 待确认清单。
- **提交**：只在你逐条确认后，才推送分支并创建 **draft PR**。

## 并行多个 PR

允许同一仓库并行推进多个 PR：每个候选另起独立会话、各开独立分支、从最新主干拉出、独立走完五道闸门。唯一限制是改动文件不重叠——动手前用 `git diff --name-only` 对照在途分支复核，重叠就排队或合并。

## 安装

```bash
mkdir -p ~/.claude/skills/oss-pr-flow
cp oss-pr-flow/SKILL.md ~/.claude/skills/oss-pr-flow/
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\oss-pr-flow"
Copy-Item oss-pr-flow\SKILL.md "$env:USERPROFILE\.claude\skills\oss-pr-flow\"
```

其他支持 `SKILL.md` 的 agent（Trae、Codex 等）同理，目录名与 frontmatter 的 `name` 保持一致即可。

## 使用

全流程：

```
用 oss-pr-flow 给 <owner/repo> 提个 PR
```

只审查（改动和草稿都已完成时）：

```
用 oss-pr-flow 审查这个 PR 的草稿
```

## 边界

- 不做代码 review，不判断方案好坏——只核对"说法有没有根据"。
- 未经确认不执行任何写操作：不推送、不建正式 PR、不改工作区。
