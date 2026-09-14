# 产品文档目录规范

> 本文件只管**规则**（命名、归档、双轨映射）。
> **清单与状态**（哪份是什么版本、当前是否有效）以 [`../README.md`](../README.md) 为唯一权威——避免两处维护同一份清单。

---

## 一、本目录是什么

`docs/product/` 是**面向人的产品文档门户**，保存产品全阶段文档的**规范化快照**。

- 权威产物原位在 `_bmad-output/planning-artifacts/`（BMAD 工具的输出空间）；
- 本目录是其**可读、可移交、可独立版本化**的副本；
- 两侧内容必须**逐字节一致**（md5 校验，见第五节）。

## 二、目录结构

```
docs/product/
├── README.md                    ← 本文件：规则
├── brief.md                     ← 阶段主文档（BRIEF）
├── brief-addendum.md            ← 阶段附册（BRIEF 的玩法设计详案）
├── prd.md                       ← 阶段主文档（PRD）
├── prd-addendum.md              ← 阶段附册（PRD 的技术选型与决策档案）
└── _archive/                    ← 历史版本，只读
    ├── brief-v0.1.md … brief-v0.5.md
    ├── brief-addendum-v0.3.md … brief-addendum-v0.5.md
    ├── prd-v0.5.md
    └── prd-addendum-v0.5.md
```

**顶层只放当前有效文档**（4 份），历史版本一律下沉到 `_archive/`。

## 三、命名规范

| 类型 | 命名格式 | 示例 |
|---|---|---|
| 阶段主文档 | `<阶段>.md` | `brief.md`、`prd.md` |
| 阶段附册 | `<阶段>-addendum.md` | `brief-addendum.md`、`prd-addendum.md` |
| 历史版本 | `_archive/<阶段>[-addendum]-vX.Y.md` | `_archive/brief-v0.5.md` |

**三条硬规则**：

1. **附册必须带阶段前缀**——BRIEF 侧叫 `brief-addendum.md`，PRD 侧叫 `prd-addendum.md`。**不要用裸名 `addendum.md`**，那会造成"同名不同物"。
2. **归档文件不带 `_archive-` 前缀**——所在目录已经表达归档语义，文件内重复是冗余。
3. **版本号只出现在归档文件名里**——顶层有效文档永不带版本号（版本写在文件内 frontmatter 的 `version` 字段）。

## 四、有效性判定

| 优先级 | 判据 |
|---|---|
| **1（主判据）** | **是否位于 `_archive/` 目录内**。不在 = 有效；在 = 归档只读 |
| 2 | [`../README.md`](../README.md) 索引表的「版本 / 状态」列（🟡 = 有效，「归档」= 失效） |
| 3 | 文件内 `version:` frontmatter（仅作复核） |

> ⚠️ **不要用 `status:` 字段判有效性**——历史归档件当年未改写元数据，`status` 至今仍是 `draft`，单看它无法区分。**以位置为准。**

## 五、双轨映射（门户 ↔ 原位）

两侧的**文件名刻意不同**：门户用人类友好的对称命名，原位服从 BMAD 工具的原生命名（改名会导致工具重跑时生成同名新文件）。

| 门户（给人看） | 原位（给工具用） |
|---|---|
| `docs/product/brief.md` | `_bmad-output/planning-artifacts/briefs/brief-wasteland-choice-2026-09-13/brief.md` |
| `docs/product/brief-addendum.md` | `…/briefs/brief-wasteland-choice-2026-09-13/addendum.md` |
| `docs/product/prd.md` | `…/prds/prd-bmad-product-2026-09-14/prd.md` |
| `docs/product/prd-addendum.md` | `…/prds/prd-bmad-product-2026-09-14/addendum.md` |
| `docs/product/_archive/*` | 对应原位的 `_archive/*` |

**编辑入口**（避并发冲突）：BRIEF 侧以门户为入口，PRD 侧以原位为入口；改完**双向同步**并校验：

```bash
P="<workspace>/bmad-product"
B="$P/_bmad-output/planning-artifacts/briefs/brief-wasteland-choice-2026-09-13"
R="$P/_bmad-output/planning-artifacts/prds/prd-bmad-product-2026-09-14"

chk() { a=$(md5sum "$2"|cut -d' ' -f1); b=$(md5sum "$3"|cut -d' ' -f1); \
        [ "$a" = "$b" ] && echo "OK   $1" || echo "FAIL $1"; }
chk "brief"           "$P/docs/product/brief.md"        "$B/brief.md"
chk "brief-addendum"  "$P/docs/product/brief-addendum.md" "$B/addendum.md"
chk "prd"             "$P/docs/product/prd.md"          "$R/prd.md"
chk "prd-addendum"    "$P/docs/product/prd-addendum.md" "$R/addendum.md"
```

四对**必须全部 OK**，否则不允许提交。

## 六、归档政策

| 规则 | 说明 |
|---|---|
| **升版必归档** | 任何版本升级前，先复制旧版到 `_archive/`，**再**写入新版（Write 是覆盖式，不先归档旧版即永久丢失） |
| **两侧同步归档** | 门户与原位**各自** `_archive/` 都要放一份 |
| **增量也升版** | 即使小修订也递增版本号（v0.5 → v0.6），不复用旧号——归档链是唯一回滚路径 |
| **归档只读** | 归档是"当时的样子"的快照，供追溯论证链。要修正就改当前有效版，**禁止回改归档** |
| **PRD 也要归档** | 早期只归档 BRIEF 是遗漏，已修正 |

## 七、审计日志

各有原位目录一份 `.memlog.md`（BRIEF 侧 / PRD 侧各一），记录**谁在何时定了什么**。

- **是日志，不是文档**——只用于追溯决策来源，**不承载需求口径**；
- 口径以 `brief.md` / `prd.md` 为准；
- 由 BMAD 的 `memlog.py` 追加，格式为 `- (decision) 文本`。

## 八、升版操作

完整流程（归档 → 写新版 → 双轨同步 → md5 校验 → 记审计 → 更新索引）见 skill：

> **`bmad-doc-versioning-and-mirror`**

其中包含中文校验陷阱、`memlog` frontmatter 陷阱、FR 编号追加策略、以及索引完整性校验。

---

*最后更新：2026-09-14 · 适用于 v0.6 起的目录规范*
