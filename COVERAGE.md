# 来源与覆盖

- [ripgrep `15.2.0`](https://github.com/BurntSushi/ripgrep/tree/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f)，源码提交 `e89fff89ac9af12e8d4ce9d5fd07beb408ca730f`；根 README 写明 MIT 或 Unlicense 双许可，其他依赖需分别核对。
- 已读 README、`GUIDE.md` 自动过滤及相关选项、部分测试文件清单；完整实现及测试套件未审阅。
- 本机 `ripgrep 15.2.0 (rev e89fff89ac)` 在临时 Git 仓库运行 `ACCEPTANCE.md` 的前三项，对照了搜索范围、大小写、退出码及 JSON 事件。实际二进制版本与源码提交短哈希匹配，未核验全部构建选项。
- 尚未从配方重建；正则引擎、完整 CLI、忽略规则细节、文件系统边界、编码、性能和平台差异仍未完整提取。
