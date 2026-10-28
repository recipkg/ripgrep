# ripgrep 验收用例（上游本机样本，重建未执行）

在一次性临时 Git 仓库内准备 `.gitignore` 内容 `ignored.txt`，文件 `normal.txt` 为三行 `alpha`、`Needle one`、`needle two`，`ignored.txt` 为 `needle ignored`，`.hidden.txt` 为 `needle hidden`，`binary.dat` 为 `needle`、NUL 和 `binary`。关闭颜色和外部 rg 配置，确认外部 Git 忽略规则不会影响样本。

1. `rg --color=never -n needle .` 只返回 `normal.txt` 第 3 行；使用 `-u` 额外返回 `ignored.txt`；使用 `--hidden` 额外返回 `.hidden.txt`，忽略文件仍被过滤。比较匹配集合，不依赖目录遍历顺序。
2. `rg --color=never -n -i needle normal.txt` 返回第 2、3 行；`rg --color=never missing normal.txt` 退出 1；`rg --color=never '(' normal.txt` 退出 2，stderr 报告正则错误。
3. `rg --color=never --json needle normal.txt` 的逐行 JSON 事件包含 begin、match、end、summary；match 的 path 为 `normal.txt`、行号 3、匹配行 `needle two\n`、子匹配字节区间 0–6。不要比较耗时字段。
4. 后续应分别验收标准输入、固定字符串、编码、二进制、链接、glob 优先级、嵌套忽略规则、权限错误、多个并发文件、跨平台和完整正则语义。只有经过完整验证才能声称兼容。

## 忽略规则和显式选择

在一次性临时 Git 仓库内创建如下文件（内容为 `needle` 加换行），并禁用外部 ripgrep 配置和 Git 用户配置：

```sh
mkdir -p scope/nested
printf 'needle\n' | tee scope/plain.txt scope/x.log scope/keep.log scope/ignored-by-ignore.txt scope/rg-only.txt scope/.hidden.txt scope/nested/parent-ignored.txt scope/nested/other.log >/dev/null
printf 'needle\000binary\n' > scope/binary.dat
printf '*.log\nparent-ignored.txt\n' > scope/.gitignore
printf '!keep.log\nignored-by-ignore.txt\n' > scope/.ignore
printf '!ignored-by-ignore.txt\nrg-only.txt\n' > scope/.rgignore
printf '!parent-ignored.txt\n' > scope/nested/.ignore
```

执行 `rg --color=never -l needle scope`，不依赖结果顺序，路径集合应为 `scope/plain.txt`、`scope/keep.log`、`scope/ignored-by-ignore.txt` 和 `scope/nested/parent-ignored.txt`。`-u` 额外出现 `scope/x.log`、`scope/rg-only.txt` 和 `scope/nested/other.log`；`-uu` 再加 `scope/.hidden.txt`；`-uuu` 再加 `scope/binary.dat`。这些成功样例均返回 0。

使用 `--no-ignore-dot` 时集合为 `scope/plain.txt`、`scope/ignored-by-ignore.txt`、`scope/rg-only.txt`；使用 `--no-ignore-vcs` 时为默认集合加上 `scope/x.log` 和 `scope/nested/other.log`。使用 `-g '*.log'` 时为 `scope/keep.log`、`scope/x.log`、`scope/nested/other.log`；用 `-g '*.txt' -g '!plain.txt'` 得到 `scope/.hidden.txt`、`scope/ignored-by-ignore.txt`、`scope/nested/parent-ignored.txt`、`scope/rg-only.txt`，反转两个 `-g` 的顺序则还包括 `scope/plain.txt`。

直接搜索 `scope/x.log`、`scope/rg-only.txt`、`scope/.hidden.txt` 各返回 `needle` 行。对 `scope/binary.dat` 执行 `rg -n needle scope/binary.dat`，本机返回 0 和二进制匹配提示；加 `--text` 返回字节序列 `1:needle`、NUL、`binary`、换行。

在另一个不位于任何 Git 仓库的临时目录，创建 `.gitignore` 含 `ignored.txt`，以及各含一行 `needle` 的 `ignored.txt` 与 `plain.txt`。`rg -l needle .` 返回两个路径；加 `--no-require-git` 则仅返回 `./plain.txt`。自动对照可在独立 [验证仓库](https://github.com/recipkg/standard-library-verification) 指定 `RECIPKG_TEST_RG` 执行。以上是上游样本，只有对独立重建程序通过才是重建验收。

## 标准输入与字节输出

按 `INPUT-OUTPUT-OBSERVED.md` 检查 UTF-8 标准输入的行号、绝对字节偏移和两个子匹配字节区间；对非 UTF-8 输入和 POSIX 文件名解码 JSON 的 `bytes` 并逐字节核对。比较 `-F` 与默认正则的输出，并用存在及缺失的显式文件检查普通和安静模式的返回码。不得把耗时字段、未观察的 stderr 顺序或跨平台文件名行为硬编码为通用合同。

## 正则依赖集成

先运行 `regex` 依赖自身的验收样本，再以 ripgrep CLI 实际验证默认正则、`-F`、大小写、Unicode 和 JSON 字节区间；库适配器通过不能代替 CLI 集成。另对未覆盖的非 UTF-8 字节、逐行和多行模式、引擎切换及错误补齐上游对照。`regex` 当前仍未完整，缺少的能力不能假设由依赖自动满足。
