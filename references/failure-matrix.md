# 学习通手机技能：故障注入矩阵

| 注入故障 | 检测证据 | 处理 | 继续动作 |
|---|---|---|---|
| 题干截断 | visibleText 不完整或末尾省略 | 标记 `needs-human-review` | 跳过该题或人工补充 |
| 图片/公式缺失 | mediaRefs 缺失、图像不可读 | 标记 `incomplete` | 不生成确定答案 |
| 页面跳题 | 题号与 QuestionSnapshot 不一致 | 作废映射 | 重新 screen 并采集 |
| 元素编号变化 | action.screen 不等于 latestScreen | 丢弃动作 | 重新 screen 后定位 |
| 网络转圈 | 页面持续 loading 或空白 | 等待后 screen 一次 | 仍失败则 RECOVERING |
| 登录弹窗 | 出现登录/会话过期文字 | handoff | 用户完成后重新采集 |
| Agent 超时 | 未在超时窗口返回 | 标记 agent failure | 取消本轮，不补猜 |
| A/B 冲突 | answer 不一致 | 标记 dissent | 二次核验，仍冲突则人工 |
| pageHash 漂移 | current hash 与共识 hash 不同 | 作废共识和 ActionPlan | 回到 QUESTION |
| 题型约束失败 | 单选多答、多选非集合等 | 标记 invalid mapping | 不填写 |
| 回读失败 | 填写后高亮/输入值不匹配 | 停止执行 | REVIEW/RECOVERING |
| 提交结果未知 | 只有按钮变化，没有官方回执 | 标记 unknown | 查记录/成绩页，不重复提交 |

## 可执行断言

```text
ASSERT latestScreen exists before each phone action
ASSERT action.screen == latestScreen.id
ASSERT nextScreen captured after action
ASSERT pageHash unchanged between consensus and fill
ASSERT each verified answer has >= 2 independent solver results
ASSERT verifiedCount == count(consensus where needsUserReview == false)
ASSERT fillableCount == count(consensus where needsUserReview == false)
ASSERT Inspect.complete == true before verified
ASSERT question type constraints pass
ASSERT postcondition confirmed after each fill action
ASSERT destructiveAction => handoffConfirmed
ASSERT postSubmitReceipt exists before success=true
```

## 验收报告

```text
当前页面：
课程/任务：
已读取：
已核验：
存在分歧：
当前动作：
下一步：
是否需要用户确认：
```
