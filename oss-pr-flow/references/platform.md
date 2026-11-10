# 命令、平台差异与仓库约定

本文件只放具体命令与平台差异。判据在主干用"可观测行为"表述，不要反过来把命令名当规则。

## 仓库约定文件查找顺序（闸门三）

依次查找，命中即停：

1. `CONTRIBUTING.md`
2. `.github/CONTRIBUTING.md`
3. `docs/CONTRIBUTING.md`
4. `.github/PULL_REQUEST_TEMPLATE.md`
5. `.github/pull_request_template.md`

各仓库命名可能不同（如 `CONTRIBUTING_zh.md`、`.github/ISSUE_TEMPLATE/`），以实际存在者为准。

## 闸门一：候选核查（GitHub 类平台示例）

名额判据与在途 PR 统计命令见 `quota.md`，此处不重复。

```
# 候选：易上手 / 缺陷 / 求帮助
gh issue list --repo {owner}/{repo} --state open --label "good first issue"

# 单条核查：指派人、认领留言、更新时间、关联 PR
gh issue view <n> --repo {owner}/{repo} --comments

# 查重
gh issue list --repo {owner}/{repo} --search "<关键词>" --state all
gh pr list --repo {owner}/{repo} --search "<关键词>" --state all
```

目标主线核实：

```
git fetch origin
git switch -c fix/<slug> origin/<默认分支>
# 读实现 / 跑最小复现 / 跑相关测试
```

## 闸门二：文件级回滚

```
git checkout <基线commit> -- <实现文件>   # 取回基线版本
# 跑新增测试，确认失败
git checkout HEAD -- <实现文件>           # 恢复修复版本
git status --short                        # 确认工作区与开始时一致
```

## 闸门五：推送与创建

```
git push -u origin <branch>
gh pr create --draft --base <默认分支> --title "<标题>" --body-file <正文文件>
```

- 正文较长时用 `--body-file`，临时文件放仓库外，用完即删。
- 撞车复核：`gh pr list --repo {owner}/{repo} --search "<改动文件名>" --state open`。
- 冲突解决后：`git rebase origin/<默认分支>`，逐块保留双方逻辑，随后跑两侧测试。

## 凭据范围

推送、创建 PR、触发流水线可能需要不同授权。先用只读方式核对当前凭据覆盖范围，缺一项就补齐，不要靠反复重试试探。

## 平台差异

- 无草稿态 PR 的平台：先把正文准备完整交用户在网页端提交，不要代建正式交付物。
- 无指派人机制的平台：把"是否已有指派人"换成"评论区是否已有人表态在做"。
- 无标签机制的平台：把标签筛选换成关键词检索。
- 默认分支、分支保护、审批重置规则各仓库不同，以仓库页面声明为准。
