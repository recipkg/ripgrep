# 标准输入和 JSON 输出：本机样本

对照对象为本机 `ripgrep 15.2.0 (rev e89fff89ac)`，`--color=never`，关闭外部 ripgrep 配置；验证仓库在临时目录运行、隔离 Git 和 HOME 配置。短哈希与配方固定源码提交的前缀一致，但不足以证明二进制由该完整提交及其全部构建选项生成。另对照固定提交 [JSON 打印器说明](https://github.com/BurntSushi/ripgrep/blob/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f/crates/printer/src/json.rs)。这些是上游本机样本，不是独立重建结果。

## 标准输入、匹配和偏移

- 通过管道输入 UTF-8 `prefix\ncafé cafe\n`，执行 `rg --color=never --json 'é|e'`：依次收到 `begin`、两个 `match`、`end` 和 `summary` 事件，标准输入的路径是 `{"text":"<stdin>"}`。第二个匹配事件的行号为 2、`absolute_offset` 为 7，`lines.text` 为 `café cafe\n`；`é` 的子匹配字节区间为 `[3,5)`，末尾 `e` 为 `[9,10)`。子匹配偏移相对于该事件的 `lines` 字节序列，而不是 Unicode 字符下标；绝对偏移则是该行在输入中的字节位置。
- 标准输入 `a.b\naxb\n` 执行 `rg -n -F 'a.b'` 只输出 `1:a.b`；去掉 `-F` 则输出两行，因为 `.` 被解释为正则。两个样本均退出 0；无匹配、错误及其他正则语义须分别验收。

## 非 UTF-8 字节

- 标准输入字节 `ff 20 68 69 74 0a`，执行 `rg --json hit`：匹配事件的 `lines` 不是 `text`，而是 `bytes`，其 Base64 解码后还原原始 6 字节；子匹配 `hit` 的区间为 `[2,5)`。因此不可用有损 UTF-8 替换来构建偏移。
- Linux/POSIX 临时目录中以原始文件名字节 `62 61 64 2d ff 2e 74 78 74` 创建含 `hit\n` 的文件，显式搜索该文件时，匹配事件的 `path.bytes` Base64 解码后还原文件名字节，`lines.text` 则为 `hit\n`。Windows 文件名表达和其他非 UTF-8 路径未验证。

## 部分错误和安静模式

对于显式路径 `normal.txt`（其中第 3 行匹配 `needle`）和不存在的 `missing.txt`，本机 `rg -n needle normal.txt missing.txt` 返回 2，同时 stdout 含匹配行、stderr 指出缺失文件；收到匹配不意味着整次搜索无错误。同一输入改为 `-q` 时本机返回 0 且 stdout 为空；stderr 是否出现、其他参数顺序、并行遍历和所有错误组合不由此样本推断。

以上样本可通过独立 [标准库验证仓库](https://github.com/recipkg/standard-library-verification) 的 `test_recipes.py` 运行，`RECIPKG_TEST_RG` 可指定目标程序。仅当目标是独立重建程序并通过测试时，才能将这些样本记作重建验收；完整格式、正则、编码和平台仍未覆盖。
