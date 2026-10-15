# AEF v0.6：AI 软件工程框架

让普通人借助 AI agent 从零构建并长期维护大型软件。核心只有三样东西：

1. **文件**：AI 每次会话读的少量文件（AGENTS.md、docs/SCOPE.md、docs/GLOSSARY.md、docs/stack.md、docs/architecture.md、.aef/state.md）
2. **流程**：7 个阶段，一会话一张票
3. **护栏**：少量门禁，只管错了代价大的事

方法论见 METHOD.md。

## 本仓库目录（框架源码，不要整个拷进项目）
```
METHOD.md          # 工程论（一页）
phases/            # 7 个阶段说明
skills/aef-*/      # 10 个技能，大的技能把细节放在同目录参考文件里，按需读取
templates/         # 新项目复制用
gates/             # 门禁运行器 + 示例
```

## 装进项目后的布局
根目录只多两个文件，框架本身在隐藏目录里，项目知识在 docs/：
```
your-project/
  AGENTS.md            # AI 入口（各家 agent 都在根目录找它）
  CLAUDE.md            # 软链接到 AGENTS.md，可选
  .aef/                # 框架：升级 AEF 时覆盖 phases/ 与 templates/ 即可
    state.md           #   进度与交接
    phases/  templates/
    gates/             #   门禁，CI 调用 .aef/gates/run-all.sh
    tickets/           #   当前迭代的票
  .claude/skills/      # 技能（Codex 等用 .agents/skills/）
  docs/                # 项目自己的知识，不随框架升级覆盖
    SCOPE.md  GLOSSARY.md  stack.md  architecture.md  decisions/
  <你的代码>           # 结构由 docs/stack.md 决定
```

## 安装
```bash
R=<your-repo>
cp templates/AGENTS.md $R/ && ln -s AGENTS.md $R/CLAUDE.md   # 已有 AGENTS.md 则把“工作方式”“硬规则”两段并进去
mkdir -p $R/docs/decisions $R/.aef/templates $R/.aef/tickets
cp -n templates/{SCOPE.md,GLOSSARY.md} $R/docs/
cp -r phases gates $R/.aef/ && cp templates/state.md $R/.aef/
cp templates/{ticket.md,decision.md,module.yaml} $R/.aef/templates/
mkdir -p $R/.claude && cp -r skills $R/.claude/skills       # 或 $R/.agents/skills
chmod +x $R/.aef/gates/run-all.sh $R/.aef/gates/*/run
```

## 使用
每次会话只说：**读 AGENTS.md 和 .aef/state.md，执行 phase-NN（票 NN）。**

## 技能
| 技能 | 什么时候用 | 参考文件 |
|---|---|---|
| aef-grill | 需求或设计不清楚 | |
| aef-stack | 定技术栈与结构，生成 docs/stack.md | |
| aef-architecture | 划模块、定依赖、横切能力 | crosscutting.md |
| aef-data | 表设计、迁移、首次建表、种子数据 | data-model.md、migration.md |
| aef-plan | 切本迭代的票 | |
| aef-ticket | 做一张票、修 bug | diagnose.md |
| aef-review | 合并前评审 | |
| aef-gates | 新增或检查门禁 | |
| aef-release | 发布、首次上线加固 | harden.md |
| aef-retro | 迭代复盘、里程碑体检 | health-check.md |

## 三档采用
| 档位 | 适合 | 用哪些 |
|---|---|---|
| 轻 | 小工具、原型、一个人几周的项目 | AGENTS.md、docs/GLOSSARY.md、.aef/state.md；技能 grill、ticket；门禁只要 lint + 测试 |
| 标准 | 正式产品、多模块 | 全部文件与 7 个阶段；门禁加模块边界、迁移不可变、空库迁移 |
| 完整 | 若依级大系统、多人协作、长期维护 | 标准 + 横切能力清单、表归属门禁、发布加固、里程碑体检 |

从“轻”开始；出现第二个模块、第一张数据表上线、第二个人加入时再升一档。
