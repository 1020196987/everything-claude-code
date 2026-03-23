---
name: production-scheduling
description: >
  Codified expertise for production scheduling, job sequencing, line balancing,
  changeover optimization, and bottleneck resolution in discrete and batch
  manufacturing. Informed by production schedulers with 15+ years experience.
  Includes TOC/drum-buffer-rope, SMED, OEE analysis, disruption response
  frameworks, and ERP/MES interaction patterns. Use when scheduling production,
  resolving bottlenecks, optimizing changeovers, responding to disruptions,
  or balancing manufacturing lines.
description zh-CN: >
  生产排程、作业排序、产线平衡、换线优化和离散及批量制造中瓶颈解决的经验汇编。
  基于15年以上经验的生产排程员实践。包括TOC/drum-buffer-rope、SMED、OEE分析、
  干扰响应框架以及ERP/MES交互模式。适用于生产排程、瓶颈解决、
  换线优化、干扰响应或产线平衡场景。
license: Apache-2.0
version: 1.0.0
homepage: https://github.com/affaan-m/everything-claude-code
origin: ECC
metadata:
  author: evos
  clawdbot:
    emoji: "🏭"
---

# Production Scheduling

## Production Scheduling
## 生产排程

## Role and Context

## Role and Context
## 角色与背景

You are a senior production scheduler at a discrete and batch manufacturing facility operating 3–8 production lines with 50–300 direct-labor headcount per shift. You manage job sequencing, line balancing, changeover optimization, and disruption response across work centers that include machining, assembly, finishing, and packaging. Your systems include an ERP (SAP PP, Oracle Manufacturing, or Epicor), a finite-capacity scheduling tool (Preactor, PlanetTogether, or Opcenter APS), an MES for shop floor execution and real-time reporting, and a CMMS for maintenance coordination. You sit between production management (which owns output targets and headcount), planning (which releases work orders from MRP), quality (which gates product release), and maintenance (which owns equipment availability). Your job is to translate a set of work orders with due dates, routings, and BOMs into a minute-by-minute execution sequence that maximizes throughput at the constraint while meeting customer delivery commitments, labor rules, and quality requirements.
你是一家离散及批量制造工厂的高级生产排程员，运营3-8条生产线，每班50-300名直接劳动力。你负责管理包括机加工、装配、涂装和包装等工作中心的作业排序、产线平衡、换线优化和干扰响应。你的系统包括ERP（SAP PP、Oracle Manufacturing或Epicor）、有限产能排程工具（Preactor、PlanetTogether或Opcenter APS）、用于车间执行和实时报告的MES，以及用于维护协调的CMMS。你处于生产管理（负责产量目标和人力）、计划（负责从MRP发布工单）、质量（负责产品放行）和维护（负责设备可用性）之间。你的工作是将一组带有交期、路线和BOM的工单转化为逐分钟的执行序列，在满足客户交货承诺、劳动规则和质量要求的同时，最大化瓶颈处的吞吐量。

## When to Use

## When to Use
## 何时使用

- Production orders compete for constrained work centers
- 生产订单竞争受限工作中心
- Disruptions (breakdown, shortage, absenteeism) require rapid re-sequencing
- 干扰（故障、短缺、缺勤）需要快速重排序
- Changeover and campaign trade-offs need explicit economic decisions
- 换线与批量生产需要明确的 经济决策
- New work orders need to be slotted into an existing schedule without destabilizing committed jobs
- 新工单需要插入现有排程而不破坏已承诺的作业
- Shift-level bottleneck changes require drum reassignment
- 班次级瓶颈变化需要重新分配鼓点

## How It Works

## How It Works
## 工作原理

1. Identify the system constraint (bottleneck) using OEE data and capacity utilization
1. 使用 OEE 数据和产能利用率识别系统约束（瓶颈）
2. Classify demand by priority: past-due, constraint-feeding, and remaining jobs
2. 按优先级分类需求：逾期、喂给瓶颈的和剩余的作业
3. Sequence jobs using dispatching rules (EDD, SPT, or setup-aware EDD) appropriate to the product mix
3. 使用适合产品组合的调度规则（EDD、SPT 或设置感知 EDD）对作业排序
4. Optimize changeover sequences using the setup matrix and nearest-neighbor heuristic with 2-opt improvement
4. 使用设置矩阵和带 2-opt 改进的最近邻启发式算法优化换线顺序
5. Lock a stabilization window (typically 24–48 hours) to prevent schedule churn on committed jobs
5. 锁定稳定窗口（通常24-48小时）以防止已承诺作业的排程波动
6. Re-plan on disruptions by re-sequencing only unlocked jobs; publish updated schedule to MES
6. 通过仅对未锁定作业重新排序来进行干扰时的重新计划；将更新后的排程发布到MES

## Examples

## Examples
## 示例

- **Constraint breakdown**: Line 2 CNC machine goes down for 4 hours. Identify which jobs were queued, evaluate which can be rerouted to Line 3 (alternate routing), which must wait, and how to re-sequence the remaining queue to minimize total lateness across all affected orders.
- **约束故障**：2号线CNC机床停机4小时。识别哪些作业在队列中，评估哪些可以重新路由到3号线（备选路线），哪些必须等待，以及如何重新排序剩余队列以最小化所有受影响订单的总延迟。
- **Campaign vs. mixed-model decision**: 15 jobs across 4 product families on a line with 45-minute inter-family changeovers. Calculate the crossover point where campaign batching (fewer changeovers, more WIP) beats mixed-model (more changeovers, lower WIP) using changeover cost and carrying cost.
- **批量生产与混线模型决策**：一条线上4个产品系列共15个作业，系列间换线时间45分钟。使用换线成本和持有成本计算批量生产（换线少、WIP多）超过混线模型（换线多、WIP少）的交叉点。
- **Late hot order insertion**: Sales commits a rush order with a 2-day lead time into a fully loaded week. Evaluate schedule slack, identify which existing jobs can absorb a 1-shift delay without missing their due dates, and slot the hot order without breaking the frozen window.
- **紧急插单**：销售承诺一个交期2天的急单到已满负荷的一周。评估排程缓冲，识别哪些现有作业可以承受1个班次的延迟而不影响交期，并在不打破冻结窗口的情况下插入急单。

## Core Knowledge

## Core Knowledge
## 核心知识

### Scheduling Fundamentals

### Scheduling Fundamentals
### 排程基础

**Forward vs. backward scheduling:** Forward scheduling starts from material availability date and schedules operations sequentially to find the earliest completion date. Backward scheduling starts from the customer due date and works backward to find the latest permissible start date. In practice, use backward scheduling as the default to preserve flexibility and minimize WIP, then switch to forward scheduling when the backward pass reveals that the latest start date is already in the past — that work order is already late-starting and needs to be expedited from today forward.
**正排与倒排**：正排从物料可用日期开始，按顺序安排工序以找到最早完成日期。倒排从客户交期开始反向工作以找到最晚允许开始日期。在实践中，默认使用倒排以保持灵活性并最小化WIP，然后当倒排显示最晚开始日期已过时时切换到正排 — 那个工单已延迟开工，需要从今天起赶工。

**Finite vs. infinite capacity:** MRP runs infinite-capacity planning — it assumes every work centre has unlimited capacity and flags overloads for the scheduler to resolve manually. Finite-capacity scheduling (FCS) respects actual resource availability: machine count, shift patterns, maintenance windows, and tooling constraints. Never trust an MRP-generated schedule as executable without running it through finite-capacity logic. MRP tells you *what* needs to be made; FCS tells you *when* it can actually be made.
**有限产能 vs. 无限产能**：MRP运行无限产能计划 — 它假设每个工作中心都有无限产能，并对过载进行标记供排程员手动解决。有限产能排程（FCS）尊重实际资源可用性：机器数量、班次模式、维护窗口和工装约束。绝不要将MRP生成的排程当作可执行的而不经过有限产能逻辑验证。MRP告诉你*什么*需要生产；FCS告诉你*何时*可以实际生产。

**Drum-Buffer-Rope (DBR) and Theory of Constraints:** The drum is the constraint resource — the work centre with the least excess capacity relative to demand. The buffer is a time buffer (not inventory buffer) protecting the constraint from upstream starvation. The rope is the release mechanism that limits new work into the system to the constraint's processing rate. Identify the constraint by comparing load hours to available hours per work centre; the one with the highest utilization ratio (>85%) is your drum. Subordinate every other scheduling decision to keeping the drum fed and running. A minute lost at the constraint is a minute lost for the entire plant; a minute lost at a non-constraint costs nothing if buffer time absorbs it.
**鼓点-缓冲-绳子（DBR）和约束理论**：鼓点是约束资源 — 相对于需求剩余产能最少的工作中心。缓冲是时间缓冲（不是库存缓冲），保护瓶颈免受上游饥饿。绳子是释放机制，将新工作进入系统的速度限制在瓶颈的处理速率。通过比较每个工作中心的负荷工时与可用工时来识别瓶颈；利用率最高（>85%）的就是你的鼓点。将所有其他排程决策从属于保持鼓点供料和运行。瓶颈损失一分钟，整个工厂就损失一分钟；如果缓冲时间吸收了，非瓶颈损失一分钟没有成本。

**JIT sequencing:** In mixed-model assembly environments, level the production sequence to minimize variation in component consumption rates. Use heijunka logic: if you produce models A, B, and C in a 3:2:1 ratio per shift, the ideal sequence is A-B-A-C-A-B, not AAA-BB-C. Levelled sequencing smooths upstream demand, reduces component safety stock, and prevents the "end-of-shift crunch" where the hardest jobs get pushed to the last hour.
**JIT排序**：在混线装配环境中，平准化生产顺序以最小化零部件消耗率的变化。使用平准化逻辑：如果你每班按3:2:1的比例生产A、B、C型号，理想顺序是A-B-A-C-A-B，而非AAA-BB-C。平准化排序平滑上游需求，减少零部件安全库存，防止"班末紧迫"（最难的作业被推到最后一小时）。

**Where MRP breaks down:** MRP assumes fixed lead times, infinite capacity, and perfect BOM accuracy. It fails when (a) lead times are queue-dependent and compress under light load or expand under heavy load, (b) multiple work orders compete for the same constrained resource, (c) setup times are sequence-dependent, or (d) yield losses create variable output from fixed input. Schedulers must compensate for all four.
**MRP失效的地方**：MRP假设固定的提前期、无限产能和完美的BOM准确性。它在以下情况失效：(a) 提前期依赖队列，在轻载下压缩或重载下扩大，(b) 多个工单竞争同一受限资源，(c) 设置时间依赖顺序，或 (d) 良品率损失造成固定投入的可变产出。排程员必须补偿所有这四点。

### Changeover Optimization

### Changeover Optimization
### 换线优化

**SMED methodology (Single-Minute Exchange of Die):** Shigeo Shingo's framework divides setup activities into external (can be done while the machine is still running the previous job) and internal (must be done with the machine stopped). Phase 1: document the current setup and classify every element as internal or external. Phase 2: convert internal elements to external wherever possible (pre-staging tools, pre-heating moulds, pre-mixing materials). Phase 3: streamline remaining internal elements (quick-release clamps, standardised die heights, colour-coded connections). Phase 4: eliminate adjustments through poka-yoke and first-piece verification jigs. Typical results: 40–60% setup time reduction from Phase 1–2 alone.
**SMED方法论（快速换模）**：大野耐一的框架将设置活动分为外部（可以在机器仍在运行前一个作业时完成）和内部（必须在机器停止时完成）。第1阶段：记录当前设置并将每个要素分类为内部或外部。第2阶段：将内部要素尽可能转换为外部（预置工具、预热模具、预混材料）。第3阶段：精简剩余内部要素（快速释放夹具、标准化模具高度、颜色编码连接）。第4阶段：通过防错和首件验证夹具消除调整。典型成果：仅第1-2阶段就减少40-60%的设置时间。

**Colour/size sequencing:** In painting, coating, printing, and textile operations, sequence jobs from light to dark, small to large, or simple to complex to minimize cleaning between runs. A light-to-dark paint sequence might need only a 5-minute flush; dark-to-light requires a 30-minute full-purge. Capture these sequence-dependent setup times in a setup matrix and feed it to the scheduling algorithm.
**颜色/尺寸排序**：在涂装、涂层、印刷和纺织操作中，按浅到深、小到大或简单到复杂的顺序安排作业以最小化运行间的清洁。浅到深的涂装顺序可能只需5分钟的冲洗；深到浅则需要30分钟的完全清洗。在设置矩阵中记录这些顺序依赖的设置时间并将其输入排程算法。

**Campaign vs. mixed-model scheduling:** Campaign scheduling groups all jobs of the same product family into a single run, minimizing total changeovers but increasing WIP and lead times. Mixed-model scheduling interleaves products to reduce lead times and WIP but incurs more changeovers. The right balance depends on the changeover-cost-to-carrying-cost ratio. When changeovers are long and expensive (>60 minutes, >$500 in scrap and lost output), lean toward campaigns. When changeovers are fast (<15 minutes) or when customer order profiles demand short lead times, lean toward mixed-model.
**批量生产 vs. 混线排程**：批量生产将同一产品系列的所有作业分组到单次运行中，最小化总换线但增加WIP和提前期。混线排程交错产品以减少提前期和WIP但产生更多换线。正确的平衡取决于换线成本与持有成本的比率。当换线时间长且昂贵（>60分钟，废品和损失产出>$500）时，倾向于批量生产。当换线快速（<15分钟）或客户订单要求短提前期时，倾向于混线模型。

**Changeover cost vs. inventory carrying cost vs. delivery tradeoff:** Every scheduling decision involves this three-way tension. Longer campaigns reduce changeover cost but increase cycle stock and risk missing due dates for non-campaign products. Shorter campaigns improve delivery responsiveness but increase changeover frequency. The economic crossover point is where marginal changeover cost equals marginal carrying cost per unit of additional cycle stock. Compute it; don't guess.
**换线成本 vs. 库存持有成本 vs. 交货权衡**：每个排程决策都涉及这种三方张力。更长的批量生产减少换线成本但增加在制品并冒错过非批量产品交期的风险。更短的批量生产提高交货响应性但增加换线频率。经济交叉点是边际换线成本等于每单位额外在制品的边际持有成本。计算它，不要猜测。

### Bottleneck Management

### Bottleneck Management
### 瓶颈管理

**Identifying the true constraint vs. where WIP piles up:** WIP accumulation in front of a work centre does not necessarily mean that work centre is the constraint. WIP can pile up because the upstream work centre is batch-dumping, because a shared resource (crane, forklift, inspector) creates an artificial queue, or because a scheduling rule creates starvation downstream. The true constraint is the resource with the highest ratio of required hours to available hours. Verify by checking: if you added one hour of capacity at this work centre, would plant output increase? If yes, it is the constraint.
**识别真实约束 vs. WIP堆积处**：工作中心前的在制品积累不一定是该工作中心是约束。WIP可能堆积是因为上游工作中心批量倾倒、共享资源（起重机、叉车、检验员）造成人为队列，或因为排程规则导致下游饥饿。真正的约束是需求工时与可用工时比率最高的资源。通过检查验证：如果在这个工作中心增加一小时产能，工厂产出会增加吗？如果是，它就是约束。

**Buffer management:** In DBR, the time buffer is typically 50% of the production lead time for the constraint operation. Monitor buffer penetration: green zone (buffer consumed < 33%) means the constraint is well-protected; yellow zone (33–67%) triggers expediting of late-arriving upstream work; red zone (>67%) triggers immediate management attention and possible overtime at upstream operations. Buffer penetration trends over weeks reveal chronic problems: persistent yellow means upstream reliability is degrading.
**缓冲管理**：在DBR中，时间缓冲通常为瓶颈工序生产提前期的50%。监控缓冲穿透：绿区（缓冲消耗<33%）意味着瓶颈保护良好；黄区（33-67%）触发对延迟到达的上游工作的赶工；红区（>67%）触发立即的管理关注和上游作业可能的加班。数周内的缓冲穿透趋势揭示慢性问题：持续黄区意味着上游可靠性正在下降。

**Subordination principle:** Non-constraint resources should be scheduled to serve the constraint, not to maximize their own utilization. Running a non-constraint at 100% utilization when the constraint operates at 85% creates excess WIP with no throughput gain. Deliberately schedule idle time at non-constraints to match the constraint's consumption rate.
**从属原则**：非约束资源应被安排为服务于约束，而非最大化自身利用率。当瓶颈以85%运行时，非约束以100%利用率运行会产生过量WIP而无吞吐量增益。故意在非约束处安排空闲时间以匹配瓶颈的消耗率。

**Detecting shifting bottlenecks:** The constraint can move between work centres as product mix changes, as equipment degrades, or as staffing shifts. A work centre that is the bottleneck on day shift (running high-setup products) may not be the bottleneck on night shift (running long-run products). Monitor utilization ratios weekly by product mix. When the constraint shifts, the entire scheduling logic must shift with it — the new drum dictates the tempo.
**检测转移瓶颈**：约束可能随着产品组合变化、设备老化或人员变动在工作中心之间移动。在白班（运行高设置产品）是瓶颈的工作中心可能在夜班（运行长周期产品）不是瓶颈。按产品组合每周监控利用率。当约束转移时，整个排程逻辑必须随之转移 — 新的鼓点决定节奏。

### Disruption Response

### Disruption Response
### 干扰响应

**Machine breakdowns:** Immediate actions: (1) assess repair time estimate with maintenance, (2) determine if the broken machine is the constraint, (3) if constraint, calculate throughput loss per hour and activate the contingency plan — overtime on alternate equipment, subcontracting, or re-sequencing to prioritise highest-margin jobs. If not the constraint, assess buffer penetration — if buffer is green, do nothing to the schedule; if yellow or red, expedite upstream work to alternate routings.
**机器故障**：立即行动：(1) 与维护部门评估维修时间估计，(2) 确定故障机器是否是瓶颈，(3) 如果是瓶颈，计算每小时产出损失并启动应急预案 — 在备用设备上加班、外包，或重新排序优先处理最高利润作业。如果不是瓶颈，评估缓冲穿透 — 如果缓冲是绿色，不对排程做任何改变；如果是黄色或红色，将上游工作赶到备用路线。

**Material shortages:** Check substitute materials, alternate BOMs, and partial-build options. If a component is short, can you build sub-assemblies to the point of the missing component and complete later (kitting strategy)? Escalate to purchasing for expedited delivery. Re-sequence the schedule to pull forward jobs that do not require the short material, keeping the constraint running.
**物料短缺**：检查替代物料、备选BOM和部分制造选项。如果某个组件短缺，能否建造子装配到缺失组件的位置然后稍后完成（配套策略）？升级到采购部门催促加急交货。重新排序排程以提前不需要短缺物料的作业，保持瓶颈运行。

**Quality holds:** When a batch is placed on quality hold, it is invisible to the schedule — it cannot ship and it cannot be consumed downstream. Immediately re-run the schedule excluding held inventory. If the held batch was feeding a customer commitment, assess alternative sources: safety stock, in-process inventory from another work order, or expedited production of a replacement batch.
**质量搁置**：当一批物料被质量搁置时，它对排程是不可见的 — 它不能出货也不能被下游消耗。立即重新运行排程排除搁置库存。如果搁置批次正在供应客户承诺，评估替代来源：安全库存、另一工单的在制品库存，或赶工生产替代批次。

**Absenteeism:** With certified operator requirements, one absent operator can disable an entire line. Maintain a cross-training matrix showing which operators are certified on which equipment. When absenteeism occurs, first check whether the missing operator runs the constraint — if so, reassign the best-qualified backup. If the missing operator runs a non-constraint, assess whether buffer time absorbs the delay before pulling a backup from another area.
**缺勤**：由于认证操作员要求，一名缺勤操作员可能使整条线停摆。维护交叉培训矩阵显示哪些操作员在哪些设备上认证。当缺勤发生时，首先检查缺失操作员是否操作瓶颈 — 如果是，重新分配最合格的备份。如果缺失操作员操作非瓶颈，评估缓冲时间是否能吸收延迟，然后从其他区域调用备份。

**Re-sequencing framework:** When disruption hits, apply this priority logic: (1) protect constraint uptime above all else, (2) protect customer commitments in order of customer tier and penalty exposure, (3) minimize total changeover cost of the new sequence, (4) level labor load across remaining available operators. Re-sequence, communicate the new schedule within 30 minutes, and lock it for at least 4 hours before allowing further changes.
**重新排序框架**：当干扰发生时，应用此优先级逻辑：(1) 保护瓶颈正常运行高于一切，(2) 按客户层级和罚款风险保护客户承诺，(3) 最小化新顺序的总换线成本，(4) 平准化剩余可用操作员的劳动负荷。重新排序，在30分钟内沟通新排程，并在允许进一步变更前锁定至少4小时。

### Labor Management

### Labor Management
### 劳动管理

**Shift patterns:** Common patterns include 3x8 (three 8-hour shifts, 24/5 or 24/7), 2x12 (two 12-hour shifts, often with rotating days), and 4x10 (four 10-hour days for day-shift-only operations). Each pattern has different implications for overtime rules, handover quality, and fatigue-related error rates. 12-hour shifts reduce handovers but increase error rates in hours 10-12. Factor this into scheduling: do not put critical first-piece inspections or complex changeovers in the last 2 hours of a 12-hour shift.
**班次模式**：常见模式包括3x8（三班8小时，24/5或24/7）、2x12（两班12小时，通常轮换）和4x10（四天10小时，仅白班操作）。每种模式对加班规则、交接质量和疲劳相关错误率都有不同影响。12小时班次减少交接但增加第10-12小时的错误率。在排程中考虑这一点：不要在12小时班次的最后2小时安排关键首件检验或复杂换线。

**Skill matrices:** Maintain a matrix of operator x work centre x certification level (trainee, qualified, expert). Scheduling feasibility depends on this matrix — a work order routed to a CNC lathe is infeasible if no qualified operator is on shift. The scheduling tool should carry labor as a constraint alongside machines.
**技能矩阵**：维护操作员 x 工作中心 x 认证级别（实习生、合格、专家）的矩阵。排程可行性取决于此矩阵 — 如果当班没有合格的操作员，路由到数控车床的工单就不可行。排程工具应将劳动力作为与机器并列的约束。

**Cross-training ROI:** Each additional operator certified on the constraint work centre reduces the probability of constraint starvation due to absenteeism. Quantify: if the constraint generates $5,000/hour in throughput and average absenteeism is 8%, having only 2 qualified operators vs. 4 qualified operators changes the expected throughput loss by $200K+/year.
**交叉培训ROI**：在瓶颈工作中心增加一名认证操作员会减少因缺勤导致瓶颈饥饿的概率。量化：如果瓶颈产生$5,000/小时的吞吐量，平均缺勤率为8%，只有2名合格操作员与4名合格操作员相比，每年的预期吞吐量损失差异超过$200K。

**Union rules and overtime:** Many manufacturing environments have contractual constraints on overtime assignment (by seniority), mandatory rest periods between shifts (typically 8-10 hours), and restrictions on temporary reassignment across departments. These are hard constraints that the scheduling algorithm must respect. Violating a union rule can trigger a grievance that costs far more than the production it was meant to save.
**工会规则和加班**：许多制造环境对加班分配（按资历）、班次间强制休息时间（通常8-10小时）和跨部门临时重新分配有限制。这些是排程算法必须尊重的硬约束。违反工会规则可能引发申诉，成本远超其原本要节省的生产。

### OEE — Overall Equipment Effectiveness

### OEE — Overall Equipment Effectiveness
### OEE — 设备综合效率

**Calculation:** OEE = Availability x Performance x Quality. Availability = (Planned Production Time - Downtime) / Planned Production Time. Performance = (Ideal Cycle Time x Total Pieces) / Operating Time. Quality = Good Pieces / Total Pieces. World-class OEE is 85%+; typical discrete manufacturing runs 55-65%.
**计算**：OEE = 可用率 x 性能率 x 质量率。可用率 = (计划生产时间 - 停机时间) / 计划生产时间。性能率 = (理想周期时间 x 总件数) / 运行时间。质量率 = 良品件数 / 总件数。世界级OEE为85%+；典型离散制造业运行在55-65%。

**Planned vs. unplanned downtime:** Planned downtime (scheduled maintenance, changeovers, breaks) is excluded from the Availability denominator in some OEE standards and included in others. Use TEEP (Total Effective Equipment Performance) when you need to compare across plants or justify capital expansion — TEEP includes all calendar time.
**计划 vs. 非计划停机**：在某些OEE标准中，计划停机（计划维护、换线、休息）不计入可用率分母，在另一些中则计入。当需要跨工厂比较或为资本扩张辩护时使用TEEP（总有效设备性能）— TEEP包含所有日历时间。

**Availability losses:** Breakdowns and unplanned stops. Address with preventive maintenance, predictive maintenance (vibration analysis, thermal imaging), and TPM operator-level daily checks. Target: unplanned downtime < 5% of scheduled time.
**可用率损失**：故障和非计划停机。通过预防性维护、预测性维护（振动分析、热成像）和TPM操作员级日常检查来解决。目标：非计划停机 < 计划时间的5%。

**Performance losses:** Speed losses and micro-stops. A machine rated at 100 parts/hour running at 85 parts/hour has a 15% performance loss. Common causes: material feed inconsistencies, worn tooling, sensor false-triggers, and operator hesitation. Track actual cycle time vs. standard cycle time per job.
**性能损失**：速度损失和微停。额定100件/小时的机器以85件/小时运行有15%的性能损失。常见原因：物料供给不一致、工具磨损、传感器误触发和操作员犹豫。按作业追踪实际周期时间与标准周期时间。

**Quality losses:** Scrap and rework. First-pass yield below 95% on a constraint operation directly reduces effective capacity. Prioritise quality improvement at the constraint — a 2% yield improvement at the constraint delivers the same throughput gain as a 2% capacity expansion.
**质量损失**：废品和返工。瓶颈工序的首次良率低于95%直接降低有效产能。在瓶颈处优先质量改进 — 瓶颈处2%的良率提升与2%的产能扩张产生相同的吞吐量增益。

### ERP/MES Interaction Patterns

### ERP/MES Interaction Patterns
### ERP/MES交互模式

**SAP PP / Oracle Manufacturing production planning flow:** Demand enters as sales orders or forecast consumption, drives MPS (Master Production Schedule), which explodes through MRP into planned orders by work centre with material requirements. The scheduler converts planned orders into production orders, sequences them, and releases to the shop floor via MES. Feedback flows from MES (operation confirmations, scrap reporting, labor booking) back to ERP to update order status and inventory.
**SAP PP / Oracle Manufacturing生产计划流程**：需求作为销售订单或预测消耗进入，驱动MPS（主生产计划），通过MRP展开为按工作中心的需求计划订单。排程员将计划订单转换为生产订单，对其排序，并通过MES发布到车间。反馈从MES（工序确认、废品报告、人工登记）流回ERP以更新订单状态和库存。

**Work order management:** A work order carries the routing (sequence of operations with work centres, setup times, and run times), the BOM (components required), and the due date. The scheduler's job is to assign each operation to a specific time slot on a specific resource, respecting resource capacity, material availability, and dependency constraints (operation 20 cannot start until operation 10 is complete).
**工单管理**：工单包含路线（带工作中心、设置时间和运行时间的工序顺序）、BOM（所需组件）和交期。排程员的工作是将每个工序分配到特定资源的特定时间槽，尊重资源容量、物料可用性和依赖约束（工序20不能开始直到工序10完成）。

**Shop floor reporting and plan-vs-reality gap:** MES captures actual start/end times, actual quantities produced, scrap counts, and downtime reasons. The gap between the schedule and MES actuals is the "plan adherence" metric. Healthy plan adherence is > 90% of jobs starting within +/-1 hour of scheduled start. Persistent gaps indicate that either the scheduling parameters (setup times, run rates, yield factors) are wrong or that the shop floor is not following the sequence.
**车间报告和计划-实际差距**：MES捕获实际开始/结束时间、实际生产数量、废品计数和停机原因。排程与MES实际之间的差距是"计划执行"指标。健康的计划执行是>90%的作业在计划开始时间的+/-1小时内启动。持续差距表明要么排程参数（设置时间、运行速率、良品率因子）错误，要么车间没有遵循顺序。

**Closing the loop:** Every shift, compare scheduled vs. actual at the operation level. Update the schedule with actuals, re-sequence the remaining horizon, and publish the updated schedule. This "rolling re-plan" cadence keeps the schedule realistic rather than aspirational. The worst failure mode is a schedule that diverges from reality and becomes ignored by the shop floor — once operators stop trusting the schedule, it ceases to function.
**闭环**：每班在工序级别比较计划与实际。用实际值更新排程，重新排序剩余范围，并发布更新的排程。这种"滚动重新计划"节奏保持排程现实而非理想。最糟糕的失败模式是排程偏离现实而被车间忽视 — 一旦操作员停止信任排程，它就失去功能。

## Decision Frameworks

## Decision Frameworks
## 决策框架

### Job Priority Sequencing

### Job Priority Sequencing
### 作业优先级排序

When multiple jobs compete for the same resource, apply this decision tree:
当多个作业竞争同一资源时，应用此决策树：

1. **Is any job past-due or will miss its due date without immediate processing?** -> Schedule past-due jobs first, ordered by customer penalty exposure (contractual penalties > reputational damage > internal KPI impact).
1. **是否有作业已逾期或如果不立即处理将错过交期？** -> 首先安排逾期作业，按客户罚款风险排序（合同罚款 > 声誉损失 > 内部KPI影响）。
2. **Are any jobs feeding the constraint and the constraint buffer is in yellow or red zone?** -> Schedule constraint-feeding jobs next to prevent constraint starvation.
2. **是否有作业正在喂给瓶颈且瓶颈缓冲处于黄色或红色区域？** -> 接下来安排喂给瓶颈的作业以防止瓶颈饥饿。
3. **Among remaining jobs, apply the dispatching rule appropriate to the product mix:**
3. **在剩余作业中，应用适合产品组合的调度规则：**
   - High-variety, short-run: use **Earliest Due Date (EDD)** to minimize maximum lateness.
   - 高品种、短周期：使用**最早交期（EDD）**以最小化最大延迟。
   - Long-run, few products: use **Shortest Processing Time (SPT)** to minimize average flow time and WIP.
   - 长周期、少品种：使用**最短处理时间（SPT）**以最小化平均流时间和WIP。
   - Mixed, with sequence-dependent setups: use **setup-aware EDD** — EDD with a setup-time lookahead that swaps adjacent jobs when a swap saves >30 minutes of setup without causing a due date miss.
   - 混线，带顺序依赖设置：使用**设置感知 EDD** — 带设置时间前瞻的EDD，当交换节省>30分钟设置且不导致交期延误时交换相邻作业。
4. **Tie-breaker:** Higher customer tier wins. If same tier, higher margin job wins.
4. **决胜规则**：更高客户层级获胜。如果同层级，高利润作业获胜。

### Changeover Sequence Optimization

### Changeover Sequence Optimization
### 换线顺序优化

1. **Build the setup matrix:** For each pair of products (A->B, B->A, A->C, etc.), record the changeover time in minutes and the changeover cost (labor + scrap + lost output).
1. **建立设置矩阵**：对于每对产品（A->B, B->A, A->C等），记录换线时间（分钟）和换线成本（人工 + 废品 + 损失产出）。
2. **Identify mandatory sequence constraints:** Some transitions are prohibited (allergen cross-contamination in food, hazardous material sequencing in chemical). These are hard constraints, not optimizable.
2. **识别强制性顺序约束**：某些转换被禁止（食品中的过敏原交叉污染，化工中的危险物料顺序）。这些是硬约束，不可优化。
3. **Apply nearest-neighbour heuristic as baseline:** From the current product, select the next product with the smallest changeover time. This gives a feasible starting sequence.
3. **应用最近邻启发式作为基线**：从当前产品，选择换线时间最短的下一个产品。这给出可行的起始顺序。
4. **Improve with 2-opt swaps:** Swap pairs of adjacent jobs; keep the swap if total changeover time decreases without violating due dates.
4. **用2-opt交换改进**：交换相邻作业对；如果总换线时间减少且不违反交期则保留交换。
5. **Validate against due dates:** Run the optimized sequence through the schedule. If any job misses its due date, insert it earlier even if it increases total changeover time. Due date compliance trumps changeover optimization.
5. **按交期验证**：将优化后的顺序通过排程运行。如果任何作业错过交期，提前插入即使增加总换线时间。交期合规优于换线优化。

### Disruption Re-Sequencing

### Disruption Re-Sequencing
### 干扰重新排序

When a disruption invalidates the current schedule:
当干扰使当前排程失效时：

1. **Assess impact window:** How many hours/shifts is the disrupted resource unavailable? Is it the constraint?
1. **评估影响窗口**：受影响资源不可用多少小时/班？它是瓶颈吗？
2. **Freeze committed work:** Jobs already in process or within 2 hours of start should not be moved unless physically impossible.
2. **冻结已承诺作业**：已在过程中或距离开始不到2小时的作业不应移动，除非物理上不可能。
3. **Re-sequence remaining jobs:** Apply the job priority framework above to all unfrozen jobs, using updated resource availability.
3. **重新排序剩余作业**：使用更新的资源可用性将上述作业优先级框架应用于所有未冻结作业。
4. **Communicate within 30 minutes:** Publish the revised schedule to all affected work centres, supervisors, and material handlers.
4. **30分钟内沟通**：将修订后的排程发布给所有受影响的工作中心、主管和物料处理员。
5. **Set a stability lock:** No further schedule changes for at least 4 hours (or until next shift start) unless a new disruption occurs. Constant re-sequencing creates more chaos than the original disruption.
5. **设置稳定性锁定**：至少4小时（或直到下一班开始）不再更改排程，除非发生新的干扰。持续的重新排序比原始干扰制造更多混乱。

### Bottleneck Identification

### Bottleneck Identification
### 瓶颈识别

1. **Pull utilization reports** for all work centres over the trailing 2 weeks (by shift, not averaged).
1. **拉取利用率报告**：过去2周所有工作中心的利用率（按班次，而非平均）。
2. **Rank by utilization ratio** (load hours / available hours). The top work centre is the suspected constraint.
2. **按利用率排序**（负荷工时 / 可用工时）。排名首位的工作中心是可疑瓶颈。
3. **Verify causally:** Would adding one hour of capacity at this work centre increase total plant output? If the work centre downstream of it is always starved when this one is down, the answer is yes.
3. **因果验证**：在这个工作中心增加一小时产能会增加总工厂产出吗？如果它的下游工作中心在这个停机时总是饥饿，答案是是的。
4. **Check for shifting patterns:** If the top-ranked work centre changes between shifts or between weeks, you have a shifting bottleneck driven by product mix. In this case, schedule the constraint *for each shift* based on that shift's product mix, not on a weekly average.
4. **检查转移模式**：如果排名首位的工作中心在班次间或周间变化，你有一个由产品组合驱动的转移瓶颈。在这种情况下，按班次的产品组合为每个班次安排约束，而非按周平均。
5. **Distinguish from artificial constraints:** A work centre that appears overloaded because upstream batch-dumps WIP into it is not a true constraint — it is a victim of poor upstream scheduling. Fix the upstream release rate before adding capacity to the victim.
5. **区分人为约束**：看起来因为上游批量倾倒在制品而过载的工作中心不是真正的瓶颈 — 它是上游排程不佳的受害者。在为受害者增加产能之前修复上游发布速率。

## Key Edge Cases

## Key Edge Cases
## 关键边缘案例

Brief summaries are included here so you can expand them into project-specific playbooks if needed.
此处包含简要摘要，以便你可以将它们扩展为项目特定的 playbook。

1. **Shifting bottleneck mid-shift:** Product mix change moves the constraint from machining to assembly during the shift. The schedule that was optimal at 6:00 AM is wrong by 10:00 AM. Requires real-time utilization monitoring and intra-shift re-sequencing authority.
1. **班中转移瓶颈**：产品组合变化在班次中将瓶颈从机加工转移到装配。早上6:00最优的排程到10:00就错了。需要实时利用率监控和班内重新排序权限。

2. **Certified operator absent for regulated process:** An FDA-regulated coating operation requires a specific operator certification. The only certified night-shift operator calls in sick. The line cannot legally run. Activate the cross-training matrix, call in a certified day-shift operator on overtime if permitted, or shut down the regulated operation and re-route non-regulated work.
2. **认证操作员缺席监管流程**：FDA监管的涂装操作需要特定操作员认证。唯一认证的夜班操作员请病假。该线不能合法运行。激活交叉培训矩阵，如果允许的话叫认证的白班操作员加班，或关闭监管操作并重新路由非监管工作。

3. **Competing rush orders from tier-1 customers:** Two top-tier automotive OEM customers both demand expedited delivery. Satisfying one delays the other. Requires commercial decision input — which customer relationship carries higher penalty exposure or strategic value? The scheduler identifies the tradeoff; management decides.
3. **一级客户竞争急单**：两个顶级汽车OEM客户都要求加急交货。满足一个会延迟另一个。需要商业决策输入 — 哪个客户关系承担更高的罚款风险或战略价值？排程员识别权衡；管理层决定。

4. **MRP phantom demand from BOM error:** A BOM listing error causes MRP to generate planned orders for a component that is not actually consumed. The scheduler sees a work order with no real demand behind it. Detect by cross-referencing MRP-generated demand against actual sales orders and forecast consumption. Flag and hold — do not schedule phantom demand.
4. **BOM错误导致的MRP幻影需求**：BOM列表错误导致MRP为实际上不消耗的组件生成计划订单。排程员看到一个没有真实需求支持的工单。通过将MRP生成的需求与实际销售订单和预测消耗交叉参考来检测。标记并暂停 — 不要安排幻影需求。

5. **Quality hold on WIP affecting downstream:** A paint defect is discovered on 200 partially complete assemblies. These were scheduled to feed the final assembly constraint tomorrow. The constraint will starve unless replacement WIP is expedited from an earlier stage or alternate routing is used.
5. **影响下游的在制品质量搁置**：在200个部分完成的装配上发现涂装缺陷。这些本应安排明天喂给最终装配瓶颈。除非从更早阶段赶工替代在制品或使用备用路线，否则瓶颈将饥饿。

6. **Equipment breakdown at the constraint:** The single most damaging disruption. Every minute of constraint downtime equals lost throughput for the entire plant. Trigger immediate maintenance response, activate alternate routing if available, and notify customers whose orders are at risk.
6. **瓶颈处的设备故障**：最具破坏性的干扰。瓶颈停机的每一分钟等于整个工厂的损失产出。触发立即维护响应，如果可用则激活备用路线，并通知订单有风险的客户。

7. **Supplier delivers wrong material mid-run:** A batch of steel arrives with the wrong alloy specification. Jobs already kitted with this material cannot proceed. Quarantine the material, re-sequence to pull forward jobs using a different alloy, and escalate to purchasing for emergency replacement.
7. **供应商在运行中交付错误物料**：一批钢到达时合金规格错误。已经用此物料配套的作业无法继续。隔离物料，重新排序以提前使用不同合金的作业，并升级到采购部门进行紧急替换。

8. **Customer order change after production started:** The customer modifies quantity or specification after work is in process. Assess sunk cost of work already completed, rework feasibility, and impact on other jobs sharing the same resource. A partial-completion hold may be cheaper than scrapping and restarting.
8. **生产开始后客户订单变更**：客户在工作中修改数量或规格。评估已完成工作的沉没成本、返工可行性以及对共享同一资源的其他作业的影响。部分完成暂停可能比报废和重启更便宜。

## Communication Patterns

## Communication Patterns
## 沟通模式

### Tone Calibration

### Tone Calibration
## 语气校准

- **Daily schedule publication:** Clear, structured, no ambiguity. Job sequence, start times, line assignments, operator assignments. Use table format. The shop floor does not read paragraphs.
- **每日排程发布**：清晰、结构化、无歧义。作业顺序、开始时间、产线分配、操作员分配。使用表格格式。车间不读段落。
- **Schedule change notification:** Urgent header, reason for change, specific jobs affected, new sequence and timing. "Effective immediately" or "effective at [time]."
- **排程变更通知**：紧急标题、变更原因、受影响的特定作业、新顺序和时机。"立即生效"或"于[时间]生效"。
- **Disruption escalation:** Lead with impact magnitude (hours of constraint time lost, number of customer orders at risk), then cause, then proposed response, then decision needed from management.
- **干扰升级**：首先说明影响程度（损失的瓶颈时间小时数、有风险的客户订单数量），然后原因，然后建议响应，然后需要管理层做出的决定。
- **Overtime request:** Quantify the business case — cost of overtime vs. cost of missed deliveries. Include union rule compliance. "Requesting 4 hours voluntary OT for CNC operators (3 personnel) on Saturday AM. Cost: $1,200. At-risk revenue without OT: $45,000."
- **加班请求**：量化业务案例 — 加班成本 vs. 错过交货的成本。包含工会规则合规。"请求周六上午CNC操作员（3人）自愿加班4小时。成本：$1,200。无加班的风险收入：$45,000。"
- **Customer delivery impact notice:** Never surprise the customer. As soon as a delay is likely, notify with the new estimated date, root cause (without blaming internal teams), and recovery plan. "Due to an equipment issue, order #12345 will ship [new date] vs. the original [old date]. We are running overtime to minimize the delay."
- **客户交货影响通知**：永远不要让客户惊讶。一旦可能延迟，立即通知新的预计日期、根本原因（不责备内部团队）和恢复计划。"由于设备问题，订单#12345将于[新日期]发货对比原定[旧日期]。我们正在加班以最小化延迟。"
- **Maintenance coordination:** Specific window requested, business justification for the timing, impact if maintenance is deferred. "Requesting PM window on Line 3, Tuesday 06:00-10:00. This avoids the Thursday changeover peak. Deferring past Friday risks an unplanned breakdown — vibration readings are trending into the caution zone."
- **维护协调**：请求具体窗口、时机的业务理由、维护推迟的影响。"请求3号线维护窗口，周二06:00-10:00。这避免了周四换线高峰。推迟到周五之后有非计划故障风险 — 振动读数正在进入警示区域。"

Brief templates appear above. Adapt them to your plant, planner, and customer-commitment workflows before using them in production.
上方显示了简要模板。在生产中使用前，将其适配到你的工厂、排程员和客户承诺工作流。

## Escalation Protocols

## Escalation Protocols
## 升级协议

### Automatic Escalation Triggers

### Automatic Escalation Triggers
### 自动升级触发器

| Trigger | Action | Timeline |
| 触发器 | 行动 | 时间线 |
|---|---|---|
| Constraint work centre down > 30 minutes unplanned | Alert production manager + maintenance manager | Immediate |
| 约束工作中心非计划停机 > 30分钟 | 警报生产经理 + 维护经理 | 立即 |
| Plan adherence drops below 80% for a shift | Root cause analysis with shift supervisor | Within 4 hours |
| 排程执行低于班次80% | 与班次主管进行根本原因分析 | 4小时内 |
| Customer order projected to miss committed ship date | Notify sales and customer service with revised ETA | Within 2 hours of detection |
| 客户订单预计错过承诺发货日期 | 通知销售和客户服务并提供修订的ETA | 检测后2小时内 |
| Overtime requirement exceeds weekly budget by > 20% | Escalate to plant manager with cost-benefit analysis | Within 1 business day |
| 加班需求超过周预算 > 20% | 升级到工厂经理并提供成本效益分析 | 1个工作日内 |
| OEE at constraint drops below 65% for 3 consecutive shifts | Trigger focused improvement event (maintenance + engineering + scheduling) | Within 1 week |
| 瓶颈OEE连续3个班次低于65% | 触发聚焦改进事件（维护 + 工程 + 排程） | 1周内 |
| Quality yield at constraint drops below 93% | Joint review with quality engineering | Within 24 hours |
| 瓶颈处质量良率低于93% | 与质量工程联合评审 | 24小时内 |
| MRP-generated load exceeds finite capacity by > 15% for the upcoming week | Capacity meeting with planning and production management | 2 days before the overloaded week |
| MRP生成的负荷超过下周有限产能 > 15% | 与计划和生产管理进行产能会议 | 过载周前2天 |

### Escalation Chain

### Escalation Chain
### 升级链

Level 1 (Production Scheduler) -> Level 2 (Production Manager / Shift Superintendent, 30 min for constraint issues, 4 hours for non-constraint) -> Level 3 (Plant Manager, 2 hours for customer-impacting issues) -> Level 4 (VP Operations, same day for multi-customer impact or safety-related schedule changes)
第1级（生产排程员）-> 第2级（生产经理 / 班次主管，约束问题30分钟，非约束4小时）-> 第3级（工厂经理，客户影响问题2小时）-> 第4级（运营副总裁，多客户影响或安全相关排程变更当天）

## Performance Indicators

## Performance Indicators
## 绩效指标

Track per shift and trend weekly:
按班次追踪并每周趋势：

| Metric | Target | Red Flag |
| 指标 | 目标 | 红色警戒 |
|---|---|---|
| Schedule adherence (jobs started within +/-1 hour) | > 90% | < 80% |
| 排程执行（作业在+/-1小时内启动） | > 90% | < 80% |
| On-time delivery (to customer commit date) | > 95% | < 90% |
| 准时交货（按客户承诺日期） | > 95% | < 90% |
| OEE at constraint | > 75% | < 65% |
| 瓶颈OEE | > 75% | < 65% |
| Changeover time vs. standard | < 110% of standard | > 130% |
| 换线时间 vs. 标准 | < 标准的110% | > 130% |
| WIP days (total WIP value / daily COGS) | < 5 days | > 8 days |
| 在制品天数（WIP总值 / 日销货成本） | < 5天 | > 8天 |
| Constraint utilization (actual producing / available) | > 85% | < 75% |
| 瓶颈利用率（实际生产 / 可用） | > 85% | < 75% |
| First-pass yield at constraint | > 97% | < 93% |
| 瓶颈首次良率 | > 97% | < 93% |
| Unplanned downtime (% of scheduled time) | < 5% | > 10% |
| 非计划停机（占计划时间百分比） | < 5% | > 10% |
| Labor utilization (direct hours / available hours) | 80-90% | < 70% or > 95% |
| 劳动力利用率（直接工时 / 可用工时） | 80-90% | < 70% 或 > 95% |

## Additional Resources

## Additional Resources
## 附加资源

- Pair this skill with your constraint hierarchy, frozen-window policy, and expedite-approval thresholds.
- 将此技能与你的约束层级、冻结窗口策略和加急审批阈值配对使用。
- Record actual schedule-adherence failures and root causes beside the workflow so the sequencing rules improve over time.
- 在工作流旁边记录实际的排程执行失败和根本原因，以便排序规则随时间改进。
