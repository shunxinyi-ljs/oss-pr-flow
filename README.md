# oss-pr-flow

## 这是什么

一个给 AI 编码助手用的**开源 PR 贡献全流程** skill。主干 `SKILL.md` 装每轮都要对照的规则，低频内容拆进 `references/` 按需加载（`quota.md` 名额判据、`checks.md` 审查清单、`review.md` review 回应规程、`templates/` 输出格式）。

从"找一个值得做的点"到"提交一个经得起 review 的 draft PR"，分成五道闸门，每道闸门不满足通过条件就停下，不带病进入下一步。

## 流程

**选题 → 修复 → 成稿 → 审查 → 提交**

- **选题**：先判名额（外部贡献者单仓库 open PR 有上限，满了就停止侦察，只推进价值最高的候选）。三个来源（现有 issue / 读源码 / 深度使用）并行侦察，允许零产出，找不到就如实结束。选序按影响面、被驳回风险、证据强度，不按"哪个容易写"。issue 与 PR 原子化：同一次运行内一起发出，避免开了 issue 被别人抢先实现。改动上限只计生产代码行数。
- **修复**：先复现出失败再改代码。反向验证写死方法：checkout 基线版本的源文件再跑测试，禁用 stash 和注释法（两者都会产生假绿）。mock 制造的失败路径必须逐行核实真实调用链存在；带 `skipif` 的用例必须说明缺依赖环境下如何被覆盖，做不到就改 monkeypatch。区分"修复验证"与"护栏用例"。
- **成稿**：按仓库自己的模板章节起草，没有模板才用最小集；只写有证据的内容。
- **审查**：忘掉作者身份，重新从 diff 和测试输出取证。每句断言按证据分级（E1 直接 / E2 间接 / E3 推断 / E0 无据），降级前必须先搜过。除事实核查外还有设计缺口三问：边界值、异常分类、契约位置（含下游消费与可观测性）。产出终稿 + 待确认清单。
- **提交**：只在你逐条确认后，才推送分支并创建 **draft PR**。收到 review 后按状态语义回应：`COMMENTED` 不是否决，`CHANGES_REQUESTED` 才卡住；fork 首次贡献者 CI 的 `action_required` 是等批准不是失败；有未处理的行内评论时不 rebase。

## 并行多个 PR

允许同一仓库并行推进多个 PR：每个候选另起独立会话、各开独立分支、从最新主干拉出、独立走完五道闸门。两个限制：改动文件不重叠（动手前用 `git diff --name-only` 对照在途分支复核）；同一缺陷只开一个 PR。

## 安装

```bash
mkdir -p ~/.claude/skills/oss-pr-flow
cp -r oss-pr-flow/* ~/.claude/skills/oss-pr-flow/
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\oss-pr-flow"
Copy-Item -Recurse oss-pr-flow\* "$env:USERPROFILE\.claude\skills\oss-pr-flow\"
```

注意要把 `references/` 一起复制，主干会在对应闸门按需加载它们。其他支持 `SKILL.md` 的 agent（Trae、Codex 等）同理，目录名与 frontmatter 的 `name` 保持一致即可。

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
