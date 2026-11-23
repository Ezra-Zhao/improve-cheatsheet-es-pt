# improve 速查表 | improve Cheatsheet | Guía rápida de improve

> 📚 **Colección / Coleção**: esta hoja forma parte de [cheatsheets](https://github.com/Ezra-Zhao/cheatsheets) — 100 hojas de referencia en un solo repositorio. / Esta folha faz parte de [cheatsheets](https://github.com/Ezra-Zhao/cheatsheets) — 100 folhas de referência em um só repositório. ⭐

**计划才是产品，代码让便宜模型去写——先看第一屏：十一条命令先用哪条**

> 一页看懂 shadcn/improve：shadcn 出的一套 agent skill。它自己从不改代码，只让最能干的模型审整个代码库，把发现写成一份份自包含的实施计划，放进 `plans/` 目录、一件一文件、带索引与依赖顺序，再交便宜模型照计划执行。贵模型花在判断上，便宜模型花在体力活上，这张卡把这个分工讲清。

- 上游项目：[shadcn/improve](https://github.com/shadcn/improve)（2026-11-13 实测 11,728 ★ / 476 forks，MIT；0 个 release，23 个 commit，根目录只有英文 README）
- 平台：任何支持 Agent Skills 格式的编程 agent；计划是纯 markdown，人也能直接接手执行
- 本页结构：中文主文 → English → Español → Português
- 同系列：审存量代码库、出修复计划看这张；先写规格再开发新功能看 [spec-kit 速查表](https://github.com/Ezra-Zhao/spec-kit-cheatsheet-es-pt)，两张分工不重叠

---

Apoyo y más: [GitHub Sponsors](https://github.com/sponsors/Ezra-Zhao) · [Gumroad — plantillas y cuadernos que uso](https://ezraflow28.gumroad.com) · [Ko-fi](https://ko-fi.com/ezra777zhao)

## 第一屏：十一条命令，先用哪条

| 命令 | 干什么 | 什么时候先用它 |
|---|---|---|
| `/improve` | 全量审计 → 按杠杆排序的发现表 → 你挑哪几条，它写成计划 | 第一次上手、想摸清整个代码库的账 |
| `/improve quick` | 便宜档：只看热点、只报头部发现 | 只想花小钱先探个底 |
| `/improve deep` | 穷尽档：每个包、每个类别都审到 | 大仓做彻底体检、不怕贵 |
| `/improve security`（也可换 perf、tests、bugs 等） | 定向审计：只审一个类别 | 心里已有怀疑方向，只查这一类 |
| `/improve branch` | 只审当前分支改动的部分 | 提 PR 之前，把这次改动先过一遍 |
| `/improve next` | 方向建议：这个项目下一步往哪走，每条建议必须引仓内证据 | 审计之外想听发展方向 |
| `/improve plan <描述>` | 跳过审计，直接就一件事写一份计划 | 需求已经明确，不用先审 |
| `/improve review-plan <文件>` | 挑一份已有计划的毛病、把它收紧 | 计划要交给执行者之前，先复核一遍 |
| `/improve execute <计划>` | 派便宜模型在隔离 worktree 里执行，再由它复核结果 | 计划定稿、想直接跑掉 |
| `/improve reconcile` | 刷新积压计划：核对已落地的、解开卡住的、退役过时的 | 隔一段时间回来，先清一遍计划队列 |
| 任意命令加 `--issues` | 把计划同时发成 GitHub issue | 团队本来就在 issue 里管活 |

一次装法，就一行（原仓口径）：

```bash
npx skills add shadcn/improve
```

## 它怎么干活：五步

1. **侦察**：先画代码库地图——技术栈、惯例、真实的构建/测试/lint 命令，这些命令之后会成为每份计划的验证门。仓库里若有 ADR、PRD、`CONTEXT.md`、`DESIGN.md`、`PRODUCT.md` 这类意图文档，它会一并读进来，已经定过的取舍不再当成问题重复报。
2. **审计**：并行派出子代理，分九类找问题：正确性、安全、性能、测试覆盖、技术债、依赖与迁移、开发体验、文档、方向。每条发现都带 `文件:行号` 证据、影响、工作量和置信度。
3. **复核**：子代理倾向于多报，所以顾问模型会亲自重读每一处被引用的位置再给你看——假的砍掉、归错的纠正、驳回的连理由一起记录，免得下一轮又报一遍。
4. **排序**：发现按杠杆（影响 ÷ 工作量、再按置信度加权）排成表。挑哪几条做成计划，由你定。
5. **写计划**：选中的一条一个文件，落进 `plans/`，附索引、推荐顺序和依赖关系图。

## 计划为什么能交给弱模型

计划是按「最弱的执行者也能照着做」写的：一个没参加过顾问会话、可能小得多的模型接过来就能干。靠的是三条（原仓口径）：

- **上下文全内嵌**：确切文件路径、现状代码摘录、仓库惯例并附范例文件、已核实可跑的命令，全写在计划里，没有一句「如前所述」。
- **验证门是机器可查的**：每一步以一条命令加它的预期输出收尾，做没做完不用执行者自己判断。
- **硬边界写死**：明确的范围外清单，加上 STOP 条件——现实和计划对不上时，停下来报告，不许小模型临场发挥。
- 另外每份计划盖了写它那一刻的 git commit，执行者动手前先做一次漂移检查，代码变了样就先停。

## 硬规矩卡（这张卡的命门）

| 规矩 | 说明（原仓自述） |
|---|---|
| 只写 `plans/` | 它从不改源码；唯一的写入就是计划目录。执行者也只在一次性 worktree 里改 |
| 只读分析 | 它跑的命令都不能动你的工作区：只读、只搜、只分析 |
| 密钥只报位置与类型 | 发现凭据也从不复述密钥值，只报在哪、是什么类型，并一律建议轮换 |
| 让它直接实现会被拒绝 | 你叫它直接写代码，它会拒绝并指回计划，或者让你走 `execute` |
| `execute` 有刹车 | 执行在隔离 worktree 里跑，复核不过最多打回两轮；合并永远由人定，它不碰 |

## 诚实标注区

- **0 个 release、没有版本锚点**：装法是直接装 skill，没有可锁的版本号；自用要稳，自己锁到某个 commit 再用（2026-11-13 原仓实测：0 release、23 个 commit）。
- **体量仍小**：整个项目才 23 个 commit，方法新、沉淀浅，拿它审重要代码库之前，先拿小仓试一轮看产出质量。
- **势头数据**：11,728 ★ / 476 forks（2026-11-13 原仓页实测，证据 A）；昨日到今日一日涨约 297 星、昨日在 Daily Diff 榜上，属第三方统计口径（证据 B）。势头不替代判断：它省的是模型账单，前提是计划质量过关，先试再定。

---

## English (summary)

improve (11,728 stars / 476 forks as of 2026-11-13, MIT, 0 releases, 23 commits) is shadcn's agent skill built on one split: your most capable model audits the codebase and writes self-contained implementation plans into `plans/` — one file per finding, with an index and dependency order — and cheaper models execute them. The skill never edits code itself. The eleven commands: `/improve` (full audit to a leverage-ranked findings table), `quick` (cheap hotspot pass), `deep` (exhaustive), focused audits like `security` / `perf` / `tests` / `bugs`, `branch` (only what your branch changes), `next` (direction suggestions, each citing repo evidence), `plan <description>` (skip the audit, spec one thing), `review-plan <file>` (critique an existing plan), `execute <plan>` (dispatch a cheaper executor in an isolated worktree, review its diff, at most two send-back rounds, merging always stays yours), `reconcile` (refresh the plan backlog), and `--issues` on any command to also publish plans as GitHub issues. Install in one line: `npx skills add shadcn/improve`; it works in any agent supporting the Agent Skills format, and plans are plain markdown a human can execute too. The workflow maps the repo first (real build/test/lint commands become verification gates; ADRs, PRDs, `CONTEXT.md`, `DESIGN.md`, `PRODUCT.md` are ingested so settled decisions are not re-flagged), audits with parallel subagents across nine categories, then the advisor re-reads every cited location itself — false positives dropped, rejections recorded with reasons. Plans are executable by the weakest plausible model because context is fully inlined (exact paths, current-state code excerpts, repo conventions with an exemplar file, verified commands), every step ends in a machine-checkable verification gate, out-of-scope lists and STOP conditions are explicit, and each plan stamps the git commit it was written against for a drift check. Hard rules: writes only to `plans/`, read-only analysis only, never reproduces secret values (locations and credential types only, rotation always recommended), and it declines direct implementation requests. Honest notes: zero releases means no version anchor — pin a commit for serious use — and 23 commits is still a small, young project.

---

## Español (guía rápida — traducción de estudio, pendiente de revisión por un hablante nativo)

**El plan es el producto; el código que lo escriba un modelo barato. Empieza por esta pantalla: cuál de los once comandos usar primero.**

improve (11.728 estrellas / 476 forks a 13 de noviembre de 2026, MIT, 0 releases, 23 commits) es una skill de agente de shadcn con una división de trabajo clara: el modelo más capaz audita todo el repositorio y escribe planes de implementación autocontenidos en el directorio `plans/` — un archivo por hallazgo, con índice y orden de dependencias — y los modelos baratos ejecutan esos planes. La skill nunca modifica código por sí misma.

Los once comandos y cuándo va cada uno primero: `/improve` hace la auditoría completa y devuelve una tabla de hallazgos ordenada por apalancamiento (impacto dividido entre esfuerzo, ponderado por confianza), para que tú elijas cuáles se convierten en planes; `quick` es la pasada barata, solo puntos calientes y hallazgos principales; `deep` es exhaustiva, cada paquete y cada categoría; las auditorías enfocadas (`security`, `perf`, `tests`, `bugs`, …) revisan una sola categoría; `branch` audita solo lo que cambia tu rama actual, ideal antes de un PR; `next` sugiere dirección para el proyecto y cada sugerencia debe citar evidencia del propio repositorio; `plan <descripción>` se salta la auditoría y especifica una sola cosa; `review-plan <archivo>` critica y ajusta un plan existente; `execute <plan>` despacha un ejecutor barato en un worktree aislado, revisa su diff contra el plan, devuelve como máximo dos rondas de corrección y la fusión la decides siempre tú; `reconcile` refresca la cola de planes: verifica los terminados, desbloquea los atascados y retira los caducos; y cualquier comando acepta `--issues` para publicar los planes también como issues de GitHub. Instalación en una línea: `npx skills add shadcn/improve`, en cualquier agente compatible con el formato Agent Skills; los planes son markdown puro, también los puede ejecutar una persona.

El flujo tiene cinco pasos: reconocimiento (mapea el repositorio — pila, convenciones y los comandos reales de build, test y lint, que luego serán las puertas de verificación de cada plan — e ingiere documentos de intención como ADR, PRD, `CONTEXT.md`, `DESIGN.md` o `PRODUCT.md`, para no marcar como problema lo que ya fue una decisión), auditoría (subagentes en paralelo en nueve categorías: corrección, seguridad, rendimiento, cobertura de tests, deuda técnica, dependencias y migraciones, experiencia de desarrollo, documentación y dirección; cada hallazgo lleva evidencia `archivo:línea`, impacto, esfuerzo y confianza), verificación (el modelo asesor relee personalmente cada ubicación citada: descarta falsos positivos, corrige atribuciones y registra los rechazos con su motivo), priorización (la tabla ordenada por apalancamiento, y tú eliges) y redacción de planes, un archivo por hallazgo con índice y grafo de dependencias.

Los planes están escritos para el ejecutor más débil plausible: todo el contexto va dentro (rutas exactas, fragmentos del código actual, convenciones del repositorio con un archivo ejemplar, comandos verificados), cada paso termina con una puerta de verificación comprobable por máquina — un comando y su salida esperada —, la lista de «fuera de alcance» es explícita y las condiciones STOP dicen «si pasa X, detente y reporta» en lugar de dejar que un modelo pequeño improvise; además cada plan lleva sellado el commit de git contra el que se escribió, para una comprobación mecánica de deriva antes de tocar nada. Reglas duras: solo escribe en `plans/`, su análisis es de solo lectura, nunca reproduce valores de secretos (solo ubicación y tipo de credencial, y siempre recomienda rotación), y si le pides implementar directamente, se niega y te remite al plan o a `execute`. Notas honestas: no hay ningún release, así que no existe ancla de versión — para uso serio, fija un commit —; el proyecto suma solo 23 commits, es joven y conviene probarlo en un repositorio pequeño antes de auditar con él algo importante. Esta página no es traducción del README original: es una síntesis con mis propias palabras a partir de la documentación pública del proyecto. Si buscas la división con spec-kit: spec-kit sirve para especificar funciones nuevas antes de desarrollarlas; improve audita código existente y produce planes de reparación.

---

## Português (guia rápido — tradução de estudo, aguardando revisão de um falante nativo)

**O plano é o produto; o código, quem escreve é um modelo barato. Comece por esta tela: qual dos onze comandos usar primeiro.**

O improve (11.728 estrelas / 476 forks em 13 de novembro de 2026, MIT, 0 releases, 23 commits) é uma skill de agente do shadcn com uma divisão de trabalho clara: o modelo mais capaz audita o repositório inteiro e escreve planos de implementação autocontidos no diretório `plans/` — um arquivo por achado, com índice e ordem de dependências — e os modelos baratos executam esses planos. A skill nunca altera código por conta própria.

Os onze comandos e quando cada um vem primeiro: `/improve` faz a auditoria completa e devolve uma tabela de achados ordenada por alavancagem (impacto dividido pelo esforço, ponderado pela confiança), para você escolher quais viram planos; `quick` é a passada barata, só pontos quentes e achados principais; `deep` é exaustiva, cada pacote e cada categoria; as auditorias focadas (`security`, `perf`, `tests`, `bugs`, …) revisam uma categoria só; `branch` audita apenas o que o seu branch atual muda, ideal antes de um PR; `next` sugere direção para o projeto e cada sugestão precisa citar evidência do próprio repositório; `plan <descrição>` pula a auditoria e especifica uma coisa só; `review-plan <arquivo>` critica e aperta um plano existente; `execute <plan>` despacha um executor barato num worktree isolado, revisa o diff contra o plano, devolve no máximo duas rodadas de correção e a fusão é sempre decisão sua; `reconcile` atualiza a fila de planos: verifica os concluídos, desbloqueia os travados e aposenta os vencidos; e qualquer comando aceita `--issues` para publicar os planos também como issues do GitHub. Instalação em uma linha: `npx skills add shadcn/improve`, em qualquer agente compatível com o formato Agent Skills; os planos são markdown puro, uma pessoa também consegue executá-los.

O fluxo tem cinco passos: reconhecimento (mapeia o repositório — pilha, convenções e os comandos reais de build, teste e lint, que depois viram os portões de verificação de cada plano — e ingere documentos de intenção como ADR, PRD, `CONTEXT.md`, `DESIGN.md` ou `PRODUCT.md`, para não marcar como problema o que já foi decisão), auditoria (subagentes em paralelo em nove categorias: correção, segurança, desempenho, cobertura de testes, dívida técnica, dependências e migrações, experiência de desenvolvimento, documentação e direção; cada achado traz evidência `arquivo:linha`, impacto, esforço e confiança), verificação (o modelo consultor relê pessoalmente cada local citado: descarta falsos positivos, corrige atribuições e registra as rejeições com o motivo), priorização (a tabela ordenada por alavancagem, e você escolhe) e redação dos planos, um arquivo por achado, com índice e grafo de dependências.

Os planos são escritos para o executor mais fraco plausível: todo o contexto vai dentro (caminhos exatos, trechos do código atual, convenções do repositório com um arquivo exemplar, comandos verificados), cada passo termina com um portão de verificação conferível por máquina — um comando e a saída esperada —, a lista de «fora de escopo» é explícita e as condições STOP dizem «se acontecer X, pare e reporte» em vez de deixar um modelo pequeno improvisar; além disso, cada plano carrega carimbado o commit do git contra o qual foi escrito, para uma checagem mecânica de deriva antes de mexer em qualquer coisa. Regras duras: só escreve em `plans/`, a análise é somente leitura, nunca reproduz valores de segredos (só localização e tipo de credencial, e sempre recomenda rotação) e, se você pedir para implementar direto, ele recusa e aponta para o plano ou para o `execute`. Notas honestas: não há nenhum release, então não existe âncora de versão — para uso sério, fixe um commit —; o projeto soma só 23 commits, é jovem, e vale testá-lo num repositório pequeno antes de auditar algo importante com ele. Esta página não é tradução do README original: é uma síntese com as minhas próprias palavras a partir da documentação pública do projeto. Sobre a divisão com o spec-kit: o spec-kit serve para especificar funções novas antes de desenvolvê-las; o improve audita código existente e produz planos de reparo.

---

## 来源与声明

- 灵感来源 / Source / Fuente / Fonte: [shadcn/improve](https://github.com/shadcn/improve)（2026-11-13 实测 11,728 ★ / 476 forks，0 个 release，23 个 commit，许可证 MIT）
- 本仓是个人学习整理：内容用我自己的话总结自上游公开文档，不搬运原文；命令、规矩与装法以 2026-11-13 的上游 README 为准，星数以同日 GitHub 实测为准，上游迭代后请以官方文档为最终依据。
- 西语、葡语部分为学习译稿，待母语校对。Las secciones en español y portugués son traducciones de estudio pendientes de revisión nativa.
- License: MIT © 2026 Guangyi Zhao
