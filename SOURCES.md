# 来源与选取依据

- 上游：[BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep)，目标为 `15.2.0`，固定提交 [`e89fff89ac9af12e8d4ce9d5fd07beb408ca730f`](https://github.com/BurntSushi/ripgrep/tree/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f)。
- GitHub API 快照（2026-10-29）：69,555 stars；最近推送日期为 2026-10-27 UTC。星数是发现信号，不证明完整性或质量。
- 上游 README 声明 MIT 或 Unlicense；依赖及打包组件应按各自文件核对许可。
- 已读范围和固定版本样本边界见 [COVERAGE.md](COVERAGE.md)；完整实现、忽略规则、正则引擎、平台与独立重建仍未覆盖。

- 多行匹配、字面换行、dotall 和输入缓冲边界依据固定提交的 [GUIDE.md](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/GUIDE.md) 与 [crates/core/flags/defs.rs](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/crates/core/flags/defs.rs)；本机样本及 JSON 输出见 [MULTILINE-OBSERVED.md](MULTILINE-OBSERVED.md)。
