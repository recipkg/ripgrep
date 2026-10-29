# ripgrep 多行匹配观察

本记录固定上游为 ripgrep 15.2.0，提交 `e89fff89ac9af12e8d4ce9d5fd07beb408ca730f`。行为说明来自固定提交的 [GUIDE.md](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/GUIDE.md) 和 [crates/core/flags/defs.rs](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/crates/core/flags/defs.rs)。本机 `rg --version` 显示同一短提交；这不能单独证明完整构建来源。

## 已观察结果

测试文件为：`head\nalpha\nmiddle\nomega\ntail\n`。

- 默认模式遇到字面换行正则 `\n` 返回 2，并建议启用 `--multiline` 或 `-U`。
- `-U` 允许匹配跨行。模式 `alpha\nmiddle` 打印第 2、3 行，含两个行号前缀。
- `-U` 不改变点号语义：模式 `alpha.*omega` 不匹配位于多行的片段。
- `-U --multiline-dotall` 让点号匹配换行；同一模式打印第 2–4 行。
- JSON 对跨行匹配仍发出一个 match 事件。此样本的 `line_number` 为 2，`lines.text` 为 `alpha\nmiddle\n`，子匹配区间为字节 [0,12)。对闭合 stdin 管道搜索时路径为 `<stdin>`，多行 JSON 内容与文件样本相同。

## 输入与资源边界

固定上游文档说明，只有当模式可能跨行时，多行搜索才需要按连续内容处理文件；这可能增加内存并降低速度。不能 memory-map 的来源（例如 stdin）会先读到 EOF，之后才开始搜索。如果启用 `-U` 但模式本身不可能匹配换行，上游会尽量回退到逐行快速搜索。本机测试只验证短文件和已关闭的 stdin；没有测试大文件峰值内存、实时管道等待时间或吞吐。

## 尚未验证

没有测试 PCRE2 引擎、多文件并行和跨文件事件顺序、长匹配输出限制、内存压力、不同编码、文件超过系统映射上限及跨平台行为。上述局部样本不代表独立重建通过。
