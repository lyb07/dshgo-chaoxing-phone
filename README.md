# chaoxing-phone

用于 dshgo 的学习通 Android 手机流程技能，目标包名为 `com.chaoxing.mobile`。

## 功能

- 读取课程、作业和考试状态
- 逐页读取题目与选项
- 使用页面快照和 `pageHash` 防止旧控件、旧题号映射被复用
- 通过独立代理进行答案核验
- 仅填写通过核验的题目
- 提交/交卷前通过 `handoff` 保留人工确认闸门

## 运行环境

由 dshgo 注入以下手机工具：

- `mcp__phone__open`
- `mcp__phone__screen`
- `mcp__phone__tap`
- `mcp__phone__scroll`
- `mcp__phone__collect`
- `mcp__phone__type`
- `mcp__phone__handoff`

工具缺失时，技能按 `SKILL.md` 的能力降级规则运行，不猜坐标、不复用旧快照。

## 安装

将 `chaoxing-phone/` 目录放入 dshgo 的 skills 目录，或使用发布包导入。安装后重启 dshgo，使技能索引重新加载。

## 设计约束

默认路径是只读检查。图片/公式缺失、题干截断、代理冲突、页面哈希漂移和题号变化都会进入人工复核或恢复状态。最终提交不会由技能自动确认。

## 文件

- `SKILL.md`：主流程和状态机
- `references/phone-contracts.md`：快照、代理结果、共识和动作合同
- `references/failure-matrix.md`：故障注入与恢复矩阵

## 版本

当前版本：`0.3.0`。详见 [CHANGELOG.md](CHANGELOG.md)。

## 许可证

MIT，详见 [LICENSE](LICENSE)。
