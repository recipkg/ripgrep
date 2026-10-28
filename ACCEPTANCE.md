# ripgrep 验收用例（上游本机样本，重建未执行）

在一次性临时 Git 仓库内准备 `.gitignore` 内容 `ignored.txt`，文件 `normal.txt` 为三行 `alpha`、`Needle one`、`needle two`，`ignored.txt` 为 `needle ignored`，`.hidden.txt` 为 `needle hidden`，`binary.dat` 为 `needle`、NUL 和 `binary`。关闭颜色和外部 rg 配置，确认外部 Git 忽略规则不会影响样本。

1. `rg --color=never -n needle .` 只返回 `normal.txt` 第 3 行；使用 `-u` 额外返回 `ignored.txt`；使用 `--hidden` 额外返回 `.hidden.txt`，忽略文件仍被过滤。比较匹配集合，不依赖目录遍历顺序。
2. `rg --color=never -n -i needle normal.txt` 返回第 2、3 行；`rg --color=never missing normal.txt` 退出 1；`rg --color=never '(' normal.txt` 退出 2，stderr 报告正则错误。
3. `rg --color=never --json needle normal.txt` 的逐行 JSON 事件包含 begin、match、end、summary；match 的 path 为 `normal.txt`、行号 3、匹配行 `needle two\n`、子匹配字节区间 0–6。不要比较耗时字段。
4. 后续应分别验收标准输入、固定字符串、编码、二进制、链接、glob 优先级、嵌套忽略规则、权限错误、多个并发文件、跨平台和完整正则语义。只有经过完整验证才能声称兼容。
