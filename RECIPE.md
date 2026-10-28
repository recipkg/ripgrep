# ripgrep 配方（提取中）

```toml
format = "recipkg/1"

[package]
name = "ripgrep"
version = "0.1.5"
kind = "software"
description = "命令行递归正则文本搜索工具 ripgrep 的重建配方草案。"
entry = "BUILD_PROMPT.md"

[dependencies.quality]
git = "https://github.com/recipkg/recipkg.git"
rev = "41e5b55c6612a9a5a10e499b5ace6a1a3fed0f2b"
subdir = "recipes/software-quality"

[dependencies.regex]
git = "https://github.com/recipkg/regex.git"
rev = "2ef25b360c923566206b5cd3d94093e355f36a2c"
```

上游 [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep/tree/e89fff89ac9af12e8d4ce9d5fd07beb408ca730f)，标签 `15.2.0`，提交 `e89fff89ac9af12e8d4ce9d5fd07beb408ca730f`；上游说明 MIT 或 Unlicense 双许可。当前配方尚未完整提取或重建。
