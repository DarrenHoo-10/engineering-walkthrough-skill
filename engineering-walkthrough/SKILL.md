---
name: engineering-walkthrough
description: Use when Codex needs to梳理复杂工程或复杂业务链路并沉淀结构化 Markdown 文档或同步到飞书/Lark 文档, especially requests like “梳理下”, “全流程”, “状态主线”, “画流程图”, “看下怎么实现的”, “参考某流程整理文档”, “同步飞书”, or any analysis involving pre-work outline alignment, swimlane business flows, ER diagrams for related tables, Feishu whiteboards, status machines, storage tables, cron/FaaS/EventBus, TCC config, idempotency, locks, rewards/funds, prizes, coupons, points, asset consistency, robustness, or cross-repo service dependencies.
---

# Engineering Walkthrough

Use this skill to turn code reading into a durable engineering walkthrough document. The default output is a Markdown document grounded in real entrypoints, state fields, tables, side effects, and code evidence.

This `SKILL.md` is the portable source of truth for the workflow. Do not require host-specific files such as local prompt files, machine-specific spec documents, private memories, or prior chats to produce the expected quality. If companion specs exist in the host environment, treat them only as optional supplements; all mandatory behavior must come from this skill.

## Standalone Portability Contract

- The skill must work when copied as a folder containing only `SKILL.md` and optional bundled resources such as `agents/openai.yaml`; do not assume any external specs, local prompt files, or user memory.
- Reconstruct domain facts from the target repository, documents, schemas, APIs, and user-provided links every time. Do not rely on remembered business knowledge unless it is explicitly re-verified.
- If publishing tools such as Feishu/Lark whiteboards are unavailable in the new host, still finish the local canonical Markdown and report the publishing blocker. Do not silently degrade required Feishu whiteboards into Mermaid code blocks, screenshots, or static images.
- When a rule below mentions “local”, it means the current workspace or target repository of the agent running the skill, not this authoring machine.

## Core Rules

- Find the main business axis first, then add details. Prefer a stable field such as `project_status`, `mission.status`, order lifecycle, task status, or funds stage.
- Before starting the walkthrough, use multiple user interactions to align and confirm the document outline. Do not begin deep code tracing, diagram drafting, or document writing until the user confirms the outline.
- The first user-facing artifact in the outline phase must be a clear, accurate Markdown table of contents. Do not answer with a business overview, stage summary, compressed lifecycle list, or any other general synopsis before the outline directory is shown.
- Default to writing or updating a reusable Markdown document unless the user explicitly asks for only a chat answer.
- Always save the walkthrough as a local canonical Markdown document first. Do not create, update, or sync a Feishu/Lark document unless the user explicitly asks for Feishu sync after or during the task.
- Use Chinese for business actions. Keep exact method names, enum names, PSM names, field paths, table names, and config keys in English.
- Every important claim needs code evidence. Ordinary transitions may live only in diagrams, but entrypoints, state writes, DB writes, async boundaries, funds actions, idempotency, and locks need evidence.
- Business process diagrams are mandatory swimlane diagrams. Use non-swimlane diagrams only for state overview, architecture whiteboards, ER diagrams, or when the user explicitly asks for another form.
- If a chapter's process diagram would be too complex to read as one diagram, recommend splitting it into several independent diagrams before drawing, and confirm the split with the user when it changes the approved outline.
- Architecture diagrams must use concrete service, PSM, application, module, table, or external-system names as nodes by default. Layer names are grouping labels, not substitutes for real node names.
- When publishing or syncing to Feishu/Lark, every diagram in the Feishu page must be drawn as a Feishu whiteboard. Mermaid, code-block diagrams, screenshots, or static rendered images are only local drafting aids unless the user explicitly asks otherwise.
- If one chapter involves three or more related tables, include a Mermaid ER diagram for those relationships unless an earlier chapter already drew the same relationship; in that case, link or refer to the earlier ER diagram instead of redrawing it.
- Any flow involving money, rewards, prizes, coupons, points, credits, quotas, or other user/business assets must have a dedicated stability subsection. Focus on data consistency and system robustness, not just the happy-path payout or issuance.
- Do not write process meta text into the document, such as “参考某文档的写法”. The document should read as a standalone business/engineering artifact.
- For open-source, mono-repo, library, or multi-module projects, never invent microservice names. Architecture diagram nodes should use the repository/project name, build module/artifact name, package/module boundary, table name, or explicit external dependency name. Implementation classes and methods may appear only as second-line responsibility notes or in code evidence.

## Workflow

0. Align the document outline with the user.
   - First ask concise scoping questions when needed: target object, repository/path, coverage boundary, expected depth, output location, Feishu sync needs, and priority concerns such as funds, rewards, status machines, or storage.
   - Propose a concrete Markdown table of contents before starting the actual walkthrough. The outline must use real document headings (`#`, `##`, `###`) and should name expected chapters, main state axis, candidate diagrams, complex-flow split suggestions, tables/ER needs, asset stability sections, and known exclusions.
   - Keep the outline proposal as a directory, not a general explanation. Avoid paragraphs that summarize the business, lifecycle, architecture, or implementation. If context is necessary, put it as short metadata fields in the outline preface, such as main axis, sub axes, core tables, coverage boundary, and known exclusions.
   - Revise the outline after user feedback. Treat “按这个大纲”, “确认”, “开始梳理”, or equivalent wording as approval.
   - If the user already provides an outline, still restate the interpreted outline and ask for confirmation or corrections before deep work.
   - Only perform light discovery before approval when needed to identify the correct repo/module/document. Do not write the final document, draw full diagrams, or do broad code tracing before approval.

1. Locate the target document after outline approval.
   - Update an existing same-topic document if one exists.
   - Otherwise create a new file under `docs/`, `specs/`, `.trellis/tasks/<task>/`, or the user-specified location.
   - Start with metadata: update date, main axis, sub axes, core tables, final state, and coverage boundary.

2. Identify object, table, and owner.
   - Confirm the core entity, primary ID, owner service, and main table.
   - Verify whether named business terms map to local tables, RPC snapshots, upstream tables, or downstream facts.

3. Trace real entrypoints.
   - Prefer handler/RPC/HTTP/BFF/cron/FaaS/EventBus entrypoints before internal helpers.
   - For cron, always write the owning PSM and task/handler name.
   - For external callbacks, trace topic/scene, consumer or handler, service method, state event, and DB write.

4. Build the state model.
   - Choose one main state axis for document chapters.
   - List all sub axes that appear in the flow.
   - Write how the axes interact: main state/time gates sub actions, sub progress affects main state, or main terminal states make sub actions invalid.

5. Collect storage and side effects.
   - List main tables, relation tables, snapshot tables, message tables, idempotency tables, reward/funds tables, and operation logs.
   - Record write timing, important fields, unique keys, and whether the repo owns the table or only reads it by RPC.
   - If assets are involved, identify the asset ledger/fact table, external asset system, status field, idempotency key, retry path, reconciliation or compensation path, and failure semantics.

6. Split by lifecycle stages.
   - Each major stage gets a business swimlane diagram, key points, storage/status writes, async-lock-idempotency-config boundaries, and code evidence.
   - If the stage involves three or more related tables, add an ER diagram or explicitly refer to the earlier section where the same ER relationship was already drawn.
   - If the stage creates, freezes, deducts, issues, settles, refunds, unlocks, or invalidates assets, add a `稳定性与一致性` subsection for that stage.
   - Do not make one global “async/lock/idempotency/config” business chapter when those concerns belong inside each stage. A final summary index is fine.

7. Verify and finish.
   - Search for stale placeholders, wrong config keys, wrong enum values, and non-existent DDL links.
   - Save and report the local Markdown path first.
   - If the user has not explicitly requested Feishu/Lark sync, ask whether they want to sync the completed document to Feishu and whether it should create a new document or update an existing one. Do not sync until they answer.
   - If syncing to Feishu/Lark, verify all diagrams were converted to Feishu whiteboards and no diagram source code or prompt text remains in the Feishu page.
   - Mention test status honestly. For docs-only changes, say tests were not run because no code changed.

## Outline Alignment Requirements

Before doing the real walkthrough, complete a user-confirmed outline phase.

Rules:

- Use multi-turn interaction, not a one-shot assumption. Ask for missing scope, propose an outline, then revise it based on user feedback until the user confirms.
- The proposed outline must cover business background, architecture, state axes, lifecycle stages, storage/ER diagrams, async-lock-idempotency-config boundaries, asset stability sections, code evidence, and Feishu sync requirements when relevant.
- The proposed outline must be a heading-level directory, not a conceptual overview. It should be possible to paste the approved outline into the final Markdown document as headings with minimal editing.
- Do not use vague outline-only chapter names such as `概况`, `总览`, `摘要`, or `整体介绍`. Prefer concrete nouns such as `核心模块职责`, `核心数据关系`, `存储数据清单`, `稳定性设计边界`, and `核心边界与易错点`.
- Do not include explanatory bullets under each heading during the outline proposal unless the bullet is a required artifact slot, such as `候选图`, `核心表`, `主轴`, `子轴`, `覆盖边界`, or `已知排除项`.
- Keep the interaction lightweight and concrete. Do not ask broad open-ended questions when the codebase or user text already gives enough direction.
- Do not include the outline negotiation transcript in the final document. The final document may include only the approved information architecture as actual headings.
- If the user tries to jump directly into a broad walkthrough without an approved outline, pause and provide the draft outline first.

## State Enum Requirements

Before drawing the main flow, write state enum tables in the status section.

Required structure:

```markdown
### 状态字段口径
| 字段 | 作用 | 枚举类型 | 读法 |
| --- | --- | --- | --- |

### `<main_field>`：<主状态名>
| 枚举值 | 状态 | 中文含义 | 在本业务里的作用 |
| --- | --- | --- | --- |

### `<sub_field>`：<子状态名>
| 枚举值 | 子状态 | 中文含义 | 在本业务里的作用 |
| --- | --- | --- | --- |
```

Rules:

- Use real enum values from the current code, IDL, thrift generated code, or local constants.
- State the enum source or dependency version when it matters.
- Include enum states that exist but are not on the main path, and mark them as “枚举层支持，当前主线不经过”.
- Never only write `PROCESSING -> COMPLETED` or “订单状态”; always include field ownership and enum type.
- If a diagram has one state axis, say so before the diagram. If it mixes axes, every state node must include its field name, for example `mission.status: Creating -> PROCESSING`.

## Document Shape

Use this shape for full lifecycle walkthroughs:

```markdown
# <业务对象>全链路梳理

> 文档更新时间：<YYYY-MM-DD>
> 主轴：<field>
> 子轴：<field...>
> 核心表：<tables>
> 覆盖范围：<included and excluded boundaries>

## 1、业务背景与整体架构
### 1.1 整体架构图
<whiteboard type="blank"></whiteboard>
### 1.2 飞书画板绘制提示词
### 1.3 核心系统职责
### 1.4 核心数据关系

## 2、状态主线
### 2.1 状态字段口径
### 2.2 `<main_field>`：主状态
### 2.x `<sub_field>`：子状态
### 2.y 主状态流转图

## 3、<阶段 A>
### Mermaid 泳道图
### ER 图（本章涉及三张及以上有关联表且前文未画时）
### 关键点
### 状态与存储
### 异步、锁、幂等、配置
### 稳定性与一致性（涉及资金、奖品、券、积分等资产时必填）
### 代码证据

## N、存储数据清单
## N+1、稳定性设计边界
## N+2、核心边界与易错点
```

Adapt headings to the local document, but keep the information architecture.

## Architecture Diagrams

Use a Feishu whiteboard placeholder for architecture diagrams, not Mermaid call graphs.

Required layers:

- 前端层
- API层
- 服务端层
- 中间件层
- 存储层
- 外部依赖层

Rules:

- Write concrete service or system names for most nodes, including frontend apps, API/BFF/RPC services, current-service PSM, downstream PSMs, cron/FaaS owners, EventBus producers/consumers, audit systems, funds/reward systems, and external business platforms. Avoid generic labels such as “运营后台”, “API服务”, “下游服务”, or “审核平台” when a real service/application name exists.
- For open-source, mono-repo, library, SDK, framework, or multi-module codebases, use repository/project/module/artifact names as node titles. Derive them from build files such as Maven `pom.xml`, Gradle `settings.gradle`, `package.json` workspaces, Bazel targets, Go modules, Python packages, or top-level module directories. Use class names such as `RepositoryServiceImpl`, commands, handlers, repositories, or helpers only as small responsibility notes, never as the primary node title.
- If the whole codebase is one project with internal modules rather than deployable services, label nodes by project/module boundary, for example `<repo-name>`, `<module-name>`, `<artifact-id>`, storage tables, and external runtime dependencies. Do not label the diagram as separate services unless deployment evidence proves they are separate services.
- When a real concrete owner cannot be determined, mark the limitation in nearby text and use a neutral label such as “外部调用方（未在本仓库实现）”; do not substitute a made-up application/service name.
- Treat layer names as visual grouping labels only. A node should not be only a layer or role name unless the concrete owner is genuinely unknown; when unknown but important, mark the limitation in the surrounding text.
- Middleware and generic infrastructure may use component names or platform names when the exact owner is not meaningful, for example MySQL, Redis, MQ/EventBus, TCC, dlock, TOS, ImageX, or metrics/logging. For storage ownership, prefer concrete table names when the diagram is about data dependencies.
- Architecture diagrams show system boundaries and dependencies only: frontends, BFF/API, service PSM, DB tables, EventBus/FaaS/cron, storage, external systems, funds/reward systems, config dependencies.
- Do not put FSM events, helper methods, branch conditions, source call stacks, or TCC read mechanics into architecture diagrams.
- Do not draw arrows or any node-to-node service lines in architecture diagrams, including dashed dependency lines. Keep relationships readable through layers, node grouping, and the surrounding text.
- Whiteboard prompts are authoring aids. They may be kept in the local canonical Markdown as a dedicated “飞书画板绘制提示词” section, but prompt text must not be synced into Feishu documents.

## Flow Diagram Rules

- Use one top-level state overview first, then stage diagrams.
- Business process diagrams must be swimlane diagrams. Prefer Mermaid `sequenceDiagram` with explicit `participant` lanes for operator/user, current service, downstream services, EventBus/FaaS/cron, audit platform, funds platform, and storage when storage writes are central to the stage.
- If one chapter's business flow has too many lanes, branch paths, async boundaries, retries, or asset side effects for a readable single diagram, suggest splitting it into independent parts such as creation, audit, async callback, reward settlement, retry/compensation, or admin repair. Keep each split diagram independently understandable and title it by business sub-flow.
- Do not force an overlarge end-to-end diagram when splitting would make ownership boundaries, async behavior, or failure paths clearer.
- Do not use plain `flowchart TD/LR` for business process stages. Plain flowcharts are reserved for state overview diagrams, decision summaries, and non-business structural diagrams.
- Swimlanes must represent responsibility boundaries: user/operator, current service, downstream service, EventBus/FaaS/cron, audit platform, funds platform.
- Do not use private helpers, converters, builders, repositories, or TCC as swimlanes.
- Put only business-relevant nodes in diagrams: entrypoints, strong business gates, state machine events, DB writes, external side effects, idempotency boundaries, async publish/consume, funds actions, and meaningful branch conditions.
- Avoid drawing routine parameter checks, DTO conversion, normal reads, helper calls, or config reads unless they change business path.
- Async boundaries must not be drawn as synchronous return flows.
- State machine nodes must show field and transition, for example `project_status: A -> B`.

## ER Diagram Rules

- Add a Mermaid `erDiagram` inside any chapter that involves three or more related tables.
- Skip redrawing only when an earlier chapter already contains the same table relationship; then write a short reference such as “ER 关系见第 X 章”.
- Draw tables that the chapter actually uses, not every nearby table in the database.
- Include primary keys, foreign keys, unique keys, business relation keys, and important JSON fields when relationships are stored in `extra` or other JSON columns.
- ER diagrams must look like normal ER diagrams with table/entity field structures, not relationship-only line diagrams. Each participating table should include a compact field list with the primary key plus lifecycle-critical foreign keys, unique/index/business keys, status fields, and time fields used by the chapter.
- If the renderer does not support PK/FK/UK/IDX annotations, encode the constraint in field names or add an adjacent relation/constraint table. Do not drop table structure or reduce the ER diagram to only connected entity names.
- When a full column list would make the diagram unreadable, keep the ER field list focused and add a relation/constraint table immediately below it with omitted constraints, DB-enforced relationships, and logical-only relationships backed by DDL or code evidence.
- Mark logical relationships clearly when they are not enforced by DB foreign keys, for example `mission_id` business key, `biz_order_id` mapping, or `extra.related_promote_mission.promote_order_id`.
- If a chapter touches three or more tables but no relationship is found, state “未发现本章表之间存在明确关系，不绘制 ER 图” in the storage section.

## Feishu Sync Diagram Rules

When the user asks to sync, upload, publish, or update a Feishu/Lark document or wiki page, convert every diagram to a Feishu whiteboard before syncing.

This includes:

- Architecture diagrams.
- State overview diagrams.
- Business swimlane diagrams.
- ER diagrams.
- Timing, decision, dependency, retry, or compensation diagrams.
- Any Mermaid, PlantUML, Graphviz, ASCII, screenshot, or static image used as a diagram in the local draft.

Rules:

- Use Mermaid or other text diagrams only as local source material or intermediate drafts. Do not sync diagram code blocks into Feishu pages.
- Create or update a Feishu whiteboard for each diagram, then embed or link the whiteboard in the correct document position.
- Keep the diagram title and a one-sentence business takeaway in the Feishu document near the whiteboard.
- Do not sync local whiteboard prompt sections, drawing prompts, Mermaid source, or tool instructions into Feishu.
- If a diagram cannot be rendered as a whiteboard because the Feishu tool is unavailable or permission is missing, stop and report the blocker instead of silently falling back to Mermaid or screenshots.

## Feishu Sync Publishing Rules

Local Markdown is the source of truth. Feishu/Lark sync is an explicit publishing step, not the default output.

Rules:

- Always finish and save the local Markdown document before Feishu sync.
- If the user only asked to梳理 or整理文档, stop after local validation and ask whether to sync to Feishu. Offer the two choices: create a new Feishu document or update an existing Feishu document.
- Sync only after explicit user intent such as “同步飞书”, “创建飞书文档”, “更新这个飞书文档”, or a clear answer choosing create/update.
- For creating a Feishu document, require the parent location before syncing: parent wiki node, parent document, folder, space, or other valid parent identifier/link. If it is missing, ask for it and do not create the document yet.
- For updating a Feishu document, require the target document identifier/link unless it is already unambiguously known from the current thread.
- Before syncing, strip local-only authoring sections from the Feishu payload: whiteboard prompts, drawing prompts, Mermaid source blocks, local file paths that are only code evidence navigation aids, and tool instructions.
- After creating or updating, reopen or fetch the Feishu page and verify the published content.
- Verify that every local diagram has a corresponding Feishu whiteboard in the Feishu page, especially architecture diagrams, state flows, business swimlanes, ER diagrams, retry/compensation diagrams, and dependency diagrams.
- Verify that the architecture diagram is actually drawn as a whiteboard and that the architecture prompt section is absent from the Feishu page.
- If post-sync verification fails, fix the Feishu page and re-check. If the tool or permission prevents verification, report the blocker and the unverified items clearly.

## Stage Boundary Table

Each lifecycle stage should include a compact boundary table:

```markdown
### 异步、锁、幂等、配置

| 类型 | 说明 |
| --- | --- |
| 异步 | <topic / cron PSM / FaaS / callback / message side effect> |
| 锁 | <lock key and granularity> |
| 幂等 | <unique key / idempotent key / pre-read skip / external dedupe> |
| 配置 | <TCC key or switch and business effect> |
```

If a category is absent, say “未发现明确 <category>” rather than omitting it when the absence is important.

## Asset Stability Requirements

Add a dedicated `稳定性与一致性` subsection whenever a flow touches money, rewards, prizes, coupons, points, credits, quotas, budget, inventory, or other assets.

Cover these points:

- Data consistency: source of truth, asset table or ledger, external transaction ID, status field, amount/count units, before/after snapshots, and whether DB transaction boundaries cover the asset fact.
- Idempotency: business idempotency key, unique index, external request ID, replay behavior, and duplicate callback handling.
- Concurrency: lock key, optimistic version, inventory/stock check, double-spend prevention, and race behavior.
- Async robustness: retry owner, retry limit, callback/cron/FaaS compensation, timeout handling, and whether async boundaries can leave local and external states inconsistent.
- Failure semantics: what happens on partial success, payer failure, zero amount, insufficient stock, external timeout, callback loss, or status reversal.
- Reconciliation and repair: scan job, reconciliation table, manual admin operation, alert, or explicit absence of a repair path.
- Observability: important logs, metrics, alarms, operation records, or audit records.

Use a compact table when possible:

```markdown
### 稳定性与一致性

| 关注点 | 设计/边界 |
| --- | --- |
| 数据一致性 | <source of truth, status, transaction boundary> |
| 幂等防重 | <unique key / biz token / external idempotent id> |
| 并发控制 | <lock / OCC / stock check> |
| 异步健壮性 | <retry / callback / compensation> |
| 失败语义 | <partial success / zero amount / failed status> |
| 对账修复 | <cron / manual repair / not found> |
| 可观测性 | <logs / metrics / operation records> |
```

## Evidence Rules

- Evidence bullets should say what the file proves, not only list file names.
- Prefer exact entrypoint, FSM, repo, model/DDL, TCC, EventBus, cron registration, and provider files.
- For generated or dependency enum values, cite the selected dependency version or generated file path when relevant.
- Keep evidence focused; do not paste every helper if it does not change business behavior.

## Final Review Checklist

Before final response, check:

- The walkthrough started only after a user-confirmed outline, or the current turn stopped at the outline awaiting confirmation.
- The canonical walkthrough document was saved locally before any Feishu/Lark publishing.
- The document has update date, coverage, main axis, sub axes, and core tables.
- Every state axis has field ownership, enum type, real enum values, Chinese meaning, business role, and source evidence.
- Single-axis diagrams declare the field; mixed-axis diagrams label every state node with field name.
- Business process diagrams are swimlane diagrams, not plain flowcharts.
- Overly complex chapter flows were split into readable independent diagrams, or the document explicitly keeps one diagram because it remains readable.
- Chapters involving three or more related tables have an ER diagram, or explicitly refer to an earlier ER diagram for the same relationship.
- ER diagrams were checked as real ER diagrams: they include table/entity field structures, PK/FK/UK/IDX or equivalent constraint markers, and lifecycle-critical business/status/time fields; they do not degrade into relationship-only line diagrams.
- Stages include storage/status writes and async-lock-idempotency-config boundaries.
- Any stage involving money, rewards, prizes, coupons, points, or other assets has a `稳定性与一致性` subsection covering data consistency and robustness.
- TCC keys are real and placed in the relevant stage, not only in a detached global table.
- Cron entries include owning PSM.
- Architecture diagrams use a whiteboard placeholder, and business/application nodes use concrete service, PSM, application, module, table, or external-system names rather than generic role labels except for middleware or infrastructure components.
- For open-source or multi-module repositories, architecture diagram node titles were verified from repository/build module names; implementation classes and methods are not used as service names.
- Feishu/Lark sync happened only after explicit user instruction. If sync was not requested, the final response asks whether to create or update a Feishu document.
- Creating a Feishu/Lark document has a known parent location. Updating has a known target document.
- When syncing to Feishu/Lark, every diagram in the Feishu page is a Feishu whiteboard: architecture, state overview, swimlane, ER, decision, dependency, retry, and compensation diagrams.
- Feishu/Lark ER whiteboards preserve the same table/entity structures as the local ER diagrams. If Feishu or Mermaid cannot parse PK/FK markers, the synced whiteboard uses renderer-compatible field suffixes plus a nearby relation/constraint table instead of deleting field structure.
- After Feishu/Lark sync, the page was reopened or fetched and verified: every local diagram has a corresponding Feishu whiteboard, the architecture diagram is drawn, and the architecture prompt section is absent.
- Feishu pages do not contain Mermaid source, whiteboard prompt text, drawing prompts, or tool instructions; omit or delete local prompt/source sections during Feishu sync.
- The document contains no writing-process text such as “参考某文档写法”.
- Links and file paths point to real local files.
- Tests or validation status are reported honestly.
