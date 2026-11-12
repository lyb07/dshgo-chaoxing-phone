# 学习通手机 Skill：可执行合同

## PhoneAction 断言

```text
ASSERT latestScreen exists before each phone action
ASSERT action.screen == latestScreen.id
ASSERT action.target is identified by current text/element evidence
EXECUTE at most one tap/scroll/type action
ASSERT nextScreen is captured immediately after the action
ASSERT old screen and element ids are discarded
```

动作契约固定为：`screen → 识别 → 单次动作 → screen → 验证后置条件`。

## AgentResult

```json
{
  "type": "object",
  "required": ["agent", "questionId", "answer", "confidence", "evidence", "issues", "pageHash", "independent", "model"],
  "properties": {
    "agent": {"type": "string"},
    "questionId": {"type": "string"},
    "answer": {},
    "confidence": {"type": "number", "minimum": 0, "maximum": 1},
    "evidence": {"type": "array", "items": {"type": "string"}},
    "issues": {"type": "array", "items": {"type": "string"}},
    "pageHash": {"type": "string"},
    "independent": {"type": "boolean"},
    "model": {"type": "string"}
  }
}
```

断言：`independent == true`；解题代理不能读取其他代理答案；`pageHash` 必须等于题目快照哈希。

## Consensus

```json
{
  "type": "object",
  "required": ["questionId", "answer", "agreement", "confidence", "dissent", "needsUserReview", "pageHash", "verifiedCount", "fillableCount"],
  "properties": {
    "questionId": {"type": "string"},
    "answer": {},
    "agreement": {"type": "string"},
    "confidence": {"type": "number", "minimum": 0, "maximum": 1},
    "dissent": {"type": "array"},
    "needsUserReview": {"type": "boolean"},
    "pageHash": {"type": "string"},
    "verifiedCount": {"type": "integer", "minimum": 0},
    "fillableCount": {"type": "integer", "minimum": 0}
  }
}
```

共识通过条件：

```text
ASSERT pageHash == currentPageHash
ASSERT at least two independent solver results agree
ASSERT Inspect.complete == true
ASSERT PageState.valid == true
ASSERT confidence >= 0.85
ASSERT verifiedCount == count(consensus where needsUserReview == false)
ASSERT fillableCount == count(consensus where needsUserReview == false) before fill stage
```

任一断言失败，`needsUserReview=true`，答案留在内存结果中，不进入填写阶段。

## ActionPlan

```json
{
  "type": "object",
  "required": ["pageHash", "preconditions", "actions", "postconditions", "destructive"],
  "properties": {
    "pageHash": {"type": "string"},
    "preconditions": {"type": "array"},
    "actions": {"type": "array"},
    "postconditions": {"type": "array"},
    "destructive": {"type": "boolean"}
  }
}
```

执行前：`ASSERT pageHash == currentPageHash`。执行后：`ASSERT postconditions` 全部满足。`destructive == true` 时：`ASSERT handoffConfirmed == true`。

## pageHash 算法

```text
pageHash = sha256(title + "\n" + visibleText + "\n" + join("\n", sort(elements[].label))).slice(0, 12)
```

排除 `capturedAt`、坐标、电量、网络图标等瞬时字段；算法必须由所有核验角色共享。

## 成功断言

```text
ASSERT postSubmitReceipt exists before success=true
ASSERT button-clicked alone != submission-success
ASSERT unknown result -> RECOVERING, never repeat submit
```
