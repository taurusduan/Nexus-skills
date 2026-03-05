# nexus-mapper

> "你不是在写代码文档。你是在为下一个接手的 AI 建立思维基础。"

**nexus-mapper** 是一个 AI Agent Skill，让 AI 对任意本地 Git 仓库执行系统性探测，产出 `.nexus-map/` 分层知识库，供后续 AI 会话**冷启动**时快速建立上下文。

## 安装

```bash
npx skills add haaaiawd/nexus-mapper
```

安装后位于 `.agent/skills/nexus-mapper/`（或你的 skills 根目录）。

兼容 Claude Code、GitHub Copilot、Cursor、Cline 等 18+ Agent 客户端（支持 `SKILL.md` 协议）。

---

## 它做什么

执行 **PROBE 五阶段探测协议**（Profile → Reason → Object → Benchmark → Emit），对目标仓库：

1. **PROFILE** — 运行 `extract_ast.py` + `git_detective.py`，采集 AST、Git 热点、文件树
2. **REASON** — AI 阅读 README / 热点 / 文件树，识别 ≥3 个系统边界
3. **OBJECT** — 三维度（结构/演化/依赖）提出 ≥3 个带代码引证的质疑点
4. **BENCHMARK** — 逐一验证质疑，修正所有节点的 `code_path`
5. **EMIT** — 原子写入 `.nexus-map/` 知识库，Schema 校验通过后生效

产出物供任何 AI 一次 `read_file .nexus-map/INDEX.md` 即可冷启动。

---

## 前提条件

| 要求 | 说明 |
|------|------|
| Python 3.10+ | `python --version` |
| 本地 Git 仓库 | `$repo_path/.git` 必须存在 |
| shell 执行能力 | Agent 环境需支持 `run_command` |

**安装脚本依赖**（首次使用）：

```bash
pip install -r .agent/skills/nexus-mapper/scripts/requirements.txt
```

---

## 语言支持

基于 `tree-sitter-language-pack`，支持 **17+ 语言**自动 dispatch：

| 语言 | 扩展名 |
|------|--------|
| Python | `.py` |
| JavaScript / JSX | `.js` / `.jsx` |
| TypeScript / TSX | `.ts` / `.tsx` / `.mts` |
| Java | `.java` |
| Go | `.go` |
| Rust | `.rs` |
| C++ / C | `.cpp` / `.hpp` / `.cc` / `.cxx` / `.hxx` / `.c` / `.h` |
| C# | `.cs` |
| Kotlin | `.kt` |
| Ruby | `.rb` |
| Swift | `.swift` |
| Scala | `.scala` |
| PHP | `.php` |
| Lua | `.lua` |
| Elixir | `.ex` / `.exs` |

未知扩展名静默跳过，不影响其他文件分析。

---

## 产出结构

```text
.nexus-map/
├── INDEX.md                    ← AI 冷启动主入口（< 2000 tokens）
├── arch/
│   ├── systems.md              ← 系统边界 + 代码位置
│   └── dependencies.md         ← Mermaid 依赖图
├── concepts/
│   ├── concept_model.json      ← Schema V1 机器可读图谱
│   └── domains.md              ← 核心领域说明
├── hotspots/
│   └── git_forensics.md        ← Git 热点 + 耦合对
└── raw/
    ├── ast_nodes.json          ← Tree-sitter 原始数据
    ├── git_stats.json          ← Git 热点与耦合数据
    └── file_tree.txt           ← 过滤后的文件树
```

下次打开该项目时，只需要 AI 读取 `INDEX.md` 即可恢复完整上下文。

---

## 使用示例

在支持的 Agent 客户端中输入：

```
帮我分析 /Users/me/projects/my-app 这个项目，生成知识库
```

AI 将自动激活 nexus-mapper，执行 PROBE 协议，并在仓库根目录写入 `.nexus-map/`。

---

## 项目结构

```text
nexus-mapper/
├── SKILL.md              ← 技能入口（PROBE 协议以及执行守则）
├── scripts/
│   ├── extract_ast.py    ← Tree-sitter 多语言 AST 提取器
│   ├── git_detective.py  ← Git 热点与耦合对分析
│   └── requirements.txt  ← Python 依赖
└── references/
    ├── 01-probe-protocol.md    ← PROBE 各阶段详细步骤（PROFILE 门控）
    ├── 02-output-schema.md     ← 输出 Schema 规范（EMIT 门控）
    ├── 03-edge-cases.md        ← 边界案例处理（REASON 门控）
    └── 04-object-framework.md  ← 三维度质疑框架（OBJECT 门控）
```

---

## License

MIT
