# 搜索范围和忽略规则：已观察部分

本节基于 [ripgrep 15.2.0 固定提交的指南](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/GUIDE.md)及本机 `ripgrep 15.2.0 (rev e89fff89ac)` 的隔离样本。路径、输出和平台差异仍需其他构建验证；记录的可执行文件短哈希不是完整构建溯源。

## 忽略文件和搜索放宽

- 在 Git 仓库内递归搜索时，样例中 `.gitignore` 排除 `*.log` 和 `parent-ignored.txt`；`.ignore` 的 `!keep.log` 使 `keep.log` 重新纳入，子目录 `.ignore` 的 `!parent-ignored.txt` 使该子目录文件重新纳入；`.rgignore` 的 `!ignored-by-ignore.txt` 覆盖 `.ignore` 的同名排除，并单独排除 `rg-only.txt`。这反映本例的优先级，不代表所有嵌套规则、被排除的父目录或符号链接均已测试。
- `--no-ignore-dot` 在该样例不使用 `.ignore` 和 `.rgignore`，仍使用 `.gitignore`；`--no-ignore-vcs` 不使用 Git 忽略规则，但仍使用 `.ignore` 和 `.rgignore`。
- `-u` 在样例纳入忽略文件，仍排除隐藏文件及二进制；`-uu` 进一步纳入隐藏文件；`-uuu` 再将二进制按文本搜索。`--text` 单独放宽二进制处理。不同组合及检测边界仍需实测。
- 在没有 Git 仓库的隔离目录中，默认 `.gitignore` 不过滤样例文件；`--no-require-git` 使该 `.gitignore` 生效。不要据此假设其他版本、配置或目录树行为完全相同。

## 命令行 glob 和显式文件

- 在样例中 `-g '*.log'` 明确选择了原本被 `.gitignore` 忽略的 `x.log` 和 `nested/other.log`。`-g '*.txt'` 还选到了被 `.rgignore` 忽略的 `rg-only.txt` 和隐藏的 `.hidden.txt`。显式 glob 因此不能简单理解为在默认搜索结果上再筛一次。
- 多个 `-g` 按传入顺序处理；样例中 `-g '*.txt' -g '!plain.txt'` 排除 `plain.txt`，反过来 `-g '!plain.txt' -g '*.txt'` 包含它。上述是重叠规则的具体样例，目录剪枝、大小写、转义及其他 glob 语法尚未完整验证。
- 直接指定被忽略的 `x.log`、`rg-only.txt` 或隐藏 `.hidden.txt`，本机样例仍搜索并返回匹配行。显式二进制文件且匹配在第一个 NUL 前时，默认输出二进制匹配提示；加 `--text` 后输出含 NUL 的实际匹配行。不能将样例推广到 NUL 在匹配之前、不同大小的文件、所有输出模式或平台。

独立实现按 `ACCEPTANCE.md` 的路径集合、顺序反例、状态及文本字节对照；未声明的行为需要继续提取，不得因为几个例子通过就声称完成 ripgrep。
