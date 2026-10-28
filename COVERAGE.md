# 来源与覆盖

- [ripgrep `15.2.0`](https://github.com/BurntSushi/ripgrep/tree/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f)，源码提交 `e89fff89ac9af12e8d4ce9d5fd07beb408ca730f`；根 README 写明 MIT 或 Unlicense 双许可，其他依赖需分别核对。
- 已读 README、`GUIDE.md` 自动过滤及相关选项、部分测试文件清单；完整实现及测试套件未审阅。
- 本机 `ripgrep 15.2.0 (rev e89fff89ac)` 在临时 Git 仓库运行 `ACCEPTANCE.md` 的前三项，对照了搜索范围、大小写、退出码及 JSON 事件。实际二进制版本与源码提交短哈希匹配，未核验全部构建选项。
- 在隔离 Git 仓库和非仓库目录进一步对照嵌套忽略规则、`.rgignore` 和 `.ignore` 覆盖、`-u` 至 `-uuu`、命令行 glob 顺序、显式文件与二进制输入；具体样本及预期见 `SEARCH-SCOPE.md`、`ACCEPTANCE.md`。未验证本机可执行文件是否从声明的完整源码提交构建，短哈希版本字符串本身不能证明构建来源。
- 以本机二进制测试标准输入的固定字符串、Unicode JSON 字节偏移、非 UTF-8 内容和 POSIX 文件名、匹配与缺失文件的混合错误；详见 `INPUT-OUTPUT-OBSERVED.md`。源码的 JSON 打印说明与这些样本对照；Windows 和其他编码、跨文件排序、其他错误组合仍未知。
- 默认正则能力依赖固定提交的 `regex` 配方；其少量 UTF-8 字符串样本已对照上游库，但字节 API、完整语法和与 ripgrep 的集成仍未完成或重建。源码 `crates/regex/Cargo.toml` 使用 `regex-automata` 与 `regex-syntax`，不能把通用字符串 API 视为其完整替代。
- 尚未从配方重建；正则引擎、完整 CLI、忽略规则细节、文件系统边界、编码、性能和平台差异仍未完整提取。
