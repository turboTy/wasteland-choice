# BMAD 新产品开发框架 — bmad-product

> **本仓库是「WASTELAND CHOICE」（异世界末世生存互动沙盒）的 BMAD 开发工作区。**
> 产品文档入口 → [`docs/README.md`](./docs/README.md)（有哪些文档、什么版本、是否有效）
>
> 本文件是**框架层说明**：BMAD 怎么装的、目录怎么组织、怎么启动流程。
> 框架版本：BMAD v6.12.0（bmm + core，官方 CLI 非交互安装）
> 配置：HTY / 中文沟通 / 中文文档输出 ｜ 安装时间：2026-09-13
> 远程仓库：`https://github.com/turboTy/wasteland-choice`（public，默认分支 `main`）

---

## 一、目录结构（实测）

```
bmad-product/
├── _bmad/              # 框架本体（bmm / core / custom / scripts / render / _config + config.toml）
├── _bmad-output/       # BMAD 产出物原位（BRIEF / PRD / ARCH / STORIES / 代码）
│   └── planning-artifacts/
│       ├── briefs/<brief-dir>/      # brief.md + addendum.md + .memlog.md + _archive/
│       └── prds/<prd-dir>/          # prd.md  + addendum.md + .memlog.md + _archive/
├── docs/               # 面向人的文档门户（可独立移交）
│   ├── README.md                    # 文档清单与状态
│   └── product/                     # 有效文档 4 份 + README.md（目录规则）+ _archive/ 10 份
├── .agents/skills/     # ★ 29 个 BMAD skill 的真实位置
├── .gitattributes / .gitignore
└── README.md           # ← 本文件（框架层）
```

**索引分工（三级，规则只在第三级维护）**

| 文件 | 职责 | 答什么问题 |
|---|---|---|
| **本文件** | **框架层** | BMAD 怎么装的、目录怎么组织、怎么启动流程 |
| [`docs/README.md`](./docs/README.md) | **文档清单** | 有哪些文档、各是什么版本、当前是否有效 |
| [`docs/product/README.md`](./docs/product/README.md) | **目录规则** | 怎么命名、怎么归档、双轨怎么映射、怎么校验 |

**skill 落点**：BMAD v6 安装器 `--tools cursor` 实际落地的是 **`.agents/skills/`**（Agent Skills 规范）。Cursor 打开本项目后，`@bmad-xxx` 直接从 `.agents/skills/` 加载。

> 实测更正：本项目**未生成** `.cursor/` 目录。早期"`.cursor/rules/bmad` 是空占位目录"的说法已不成立，勿再按该路径排查。

---

## 二、环境前提（已在早期会话配置，无需重做）

| 项 | 状态 | 说明 |
|---|---|---|
| Cursor 安装 | ✅ | `D:\Program Files (x86)\cursor\cursor\Cursor.exe`（装在 D 盘） |
| 系统 PATH | ✅ | Cursor 目录 + uv 目录已写入**用户级** PATH |
| uv 依赖 | ✅ | `uv 0.12.13`（`bmad-build` 的 `uv run` 硬依赖，已可用） |
| git 远程 | ✅ | 推送 / 拉取均已实测通过 |

**生效要求**：改完 PATH 后须**完全退出并重启 Cursor**，新进程才读到新 PATH。

**网络说明**：`github.com` 为**时段性可达**（HTTPS 直连与走代理都可能间歇失败，`api.github.com` 通常不受影响）。推送失败时用工作区根目录的 `git-sync.sh` 在窗口期重试：

```bash
./git-sync.sh probe                              # 探测当前通道
./git-sync.sh push D:\workbuddy\2026-09-13-14-50-08\bmad-product 6
```

---

## 三、完整流程（BMAD 四阶段，展开为 7 步）

| 阶段 | 步骤 | 核心 skill（已验证存在） | 你要做的 | 产出物 |
|---|---|---|---|---|
| ① 分析 | 1. 构思 | `bmad-brainstorming` / `bmad-product-brief` | 口述想法、纠偏、补领域知识 | BRIEF |
| ② 规划 | 2. 需求 | `bmad-prd` | 评审 PRD（EARS 原则）、签字 | PRD.md |
| ③ 方案 | 3. 架构 | `bmad-architecture` | 确认技术边界 / 选型 | ARCH.md |
| ③ 方案 | 4. 拆分 | `bmad-create-epics-and-stories` | 确认故事优先级 | STORIES |
| ④ 实施 | 5. 构建 | `bmad-build` / `bmad-build-auto` | 验收代码 | 可运行代码 |
| ④ 实施 | 6. 测试 | `bmad-qa-generate-e2e-tests` | 确认用例覆盖 | E2E 测试 |
| ④ 实施 | 7. 复盘 | `bmad-retrospective` | 评审上线效果 | 复盘报告 |

> 可用辅助 skill：`bmad-advanced-elicitation`（深挖需求）、`bmad-prfaq`（产品 FAQ）、
> `bmad-ux`（交互）、`bmad-code-review`、`bmad-sprint-planning`、`bmad-correct-course`（纠偏）、`bmad-help`（总入口）。

---

## 四、你在流程中的硬纪律（人定方向、AI 执行）

1. **每阶段产出你评审签字后才进下一阶段**（PRD 不签字不进架构，以此类推）。
2. **提供领域知识**：BMAD 不替你懂业务，缺上下文会编造——这是黄级风险。
3. **做范围与优先级取舍**：Capabilities 排布、Non-goals 划界由你定，我只给选项与权衡。
4. **拍板上线/迭代决策**。

---

## 五、风险分级与过关标准

- 🔴 **必避**：PRD/ARCH 未评审签字就进 build → 大面积返工。
  过关标准：五字段（Why / Capabilities / Constraints / Non-goals / Success signal）齐全且你明确确认。
- 🟡 **注意**：① Cursor 未重启导致 `uv` 不通；② 领域知识缺失致 AI 编造。
  过关标准：重启 Cursor + 你提供业务上下文。
- 🟢 **达标**：每阶段产出你签字后才启动下一阶段。

---

## 六、启动方式（双通道）

**通道 A — Cursor 内自跑（推荐）**
1. 重启 Cursor，打开 `D:\workbuddy\2026-09-13-14-50-08\bmad-product`
2. 聊天框输入 `@bmad-brainstorming` 或 `@bmad-product-brief` 启动构思
3. 验证：`@bmad-help` 应被识别；终端 `uv --version` 返回 0.12.13

**通道 B — WorkBuddy 内由 AI 扮演角色**
我读取 `.agents/skills/` 下对应 SKILL.md，扮演 brainstorming / PM / architect / dev 等角色，
产出门逐一给你评审签字。适用于本环境无桌面会话、或不便切到 Cursor 的场景。

---

## 七、当前进度

| 阶段 | 状态 |
|---|---|
| ① 分析 — BRIEF | ✅ **v0.6-draft**（A1–A29；A25 / A26 待拍板） |
| ② 规划 — PRD | ✅ **v0.6-draft**（49 条 FR，待评审） |
| ③ 方案 / ④ 实施 | ⏸ 未启动 |

**下一步**：PRD v0.6 评审 → `bmad-ux` → `bmad-architecture`。

> 本节仅为速览。**详细文档清单、版本、评审状态以 [`docs/README.md`](./docs/README.md) 为唯一权威**，避免两处维护同一份清单。

---

## 八、安装备注（技术细节，可忽略）

本次安装末尾报 "Installation failed"，实为安装器**自身清理步骤**触发安全删除护栏
（待删 57 个目标 > 50 阈值需人工确认），**不影响已落地框架**。已实测：29 个 skill 全在
`.agents/skills/`、四阶段关键 skill 均 ✅、`_bmad-output` 已建。若需消除该告警，可手动删除
`_bmad/core/bmad-party-mode`（如有残留），非必需。
