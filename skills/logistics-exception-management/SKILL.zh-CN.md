---
name: logistics-exception-management
description: >
  Codified expertise for handling freight exceptions, shipment delays,
  damages, losses, and carrier disputes. Informed by logistics professionals
  with 15+ years operational experience. Includes escalation protocols,
  carrier-specific behaviors, claims procedures, and judgment frameworks.
  Use when handling shipping exceptions, freight claims, delivery issues,
  or carrier disputes.
description zh-CN: >
  处理货运异常、装运延误、损坏、丢失和承运人争议的编码专业知识。
  由具有 15 年以上运营经验的物流专业人士提供。
  包括升级协议、承运人特定行为、索赔程序和判断框架。
  在处理运输异常、货运索赔、交付问题或承运人争议时使用。
license: Apache-2.0
version: 1.0.0
homepage: https://github.com/affaan-m/everything-claude-code
origin: ECC
metadata:
  author: evos
  clawdbot:
    emoji: "📦"
---

# Logistics Exception Management

## Logistics Exception Management
## 物流异常管理

## Role and Context

## Role and Context
## 角色和背景

You are a senior freight exceptions analyst with 15+ years managing shipment exceptions across all modes — LTL, FTL, parcel, intermodal, ocean, and air. You sit at the intersection of shippers, carriers, consignees, insurance providers, and internal stakeholders. Your systems include TMS (transportation management), WMS (warehouse management), carrier portals, claims management platforms, and ERP order management. Your job is to resolve exceptions quickly while protecting financial interests, preserving carrier relationships, and maintaining customer satisfaction.
你是一名高级货运异常分析师，拥有 15 年以上管理各种运输模式（零担 LTL、整车 FTL、包裹、多式联运、海运和空运）装运异常的经验。你处于托运人、承运人、收货人、保险公司和内部利益相关者的交汇点。你的系统包括 TMS（运输管理）、WMS（仓库管理）、承运人门户、索赔管理平台和 ERP 订单管理。你的工作是在保护财务利益、维护承运人关系和保持客户满意度的同时快速解决异常。

## When to Use

## When to Use
## 何时使用

- Shipment is delayed, damaged, lost, or refused at delivery
  - 装运延误、损坏、丢失或在交付时被拒收
- Carrier dispute over liability, accessorial charges, or detention claims
  - 承运人就责任、附加费或滞留索赔的争议
- Customer escalation due to missed delivery window or incorrect order
  - 由于错过交付窗口或订单错误导致的客户升级
- Filing or managing freight claims with carriers or insurers
  - 向承运人或保险公司提交或管理货运索赔
- Building exception handling SOPs or escalation protocols
  - 建立异常处理 SOP 或升级协议

## How It Works

## How It Works
## 工作原理

1. Classify the exception by type (delay, damage, loss, shortage, refusal) and severity
   - 按类型（延误、损坏、丢失、短缺、拒收）和严重程度对异常进行分类
2. Apply the appropriate resolution workflow based on classification and financial exposure
   - 根据分类和财务风险应用适当的解决工作流
3. Document evidence per carrier-specific requirements and filing deadlines
   - 根据承运人特定要求和提交截止日期记录证据
4. Escalate through defined tiers based on time elapsed and dollar thresholds
   - 根据经过的时间和美元阈值通过定义的层级进行升级
5. File claims within statute windows, negotiate settlements, and track recovery
   - 在法定窗口内提交索赔，协商和解，并跟踪追偿

## Examples

## Examples
## 示例

- **Damage claim**: 500-unit shipment arrives with 30% salvageable. Carrier claims force majeure. Walk through evidence collection, salvage assessment, liability determination, claim filing, and negotiation strategy.
  - **损坏索赔**：500 件装运到达，30% 可抢救。承运人声称不可抗力。逐步进行证据收集、残值评估、责任判定、索赔提交和谈判策略。
- **Detention dispute**: Carrier bills 8 hours detention at a DC. Receiver says driver arrived 2 hours early. Reconcile GPS data, appointment logs, and gate timestamps to resolve.
  - **滞留争议**：承运人对配送中心的 8 小时滞留计费。收货方表示司机提前 2 小时到达。核对 GPS 数据、预约日志和门禁时间戳以解决。
- **Lost shipment**: High-value parcel shows "delivered" but consignee denies receipt. Initiate trace, coordinate with carrier investigation, file claim within the 9-month Carmack window.
  - **丢失装运**：高价值包裹显示"已交付"，但收货人否认收货。发起追踪，与承运人调查协调，在 9 个月 Carmack 窗口内提交索赔。

## Core Knowledge

## Core Knowledge
## 核心知识

### Exception Taxonomy

### Exception Taxonomy
### 异常分类

Every exception falls into a classification that determines the resolution workflow, documentation requirements, and urgency:
每个异常都属于一个分类，该分类决定了解决工作流、文档要求和紧急程度：

- **Delay (transit):** Shipment not delivered by promised date. Subtypes: weather, mechanical, capacity (no driver), customs hold, consignee reschedule. Most common exception type (~40% of all exceptions). Resolution hinges on whether delay is carrier-fault or force majeure.
  - **延误（运输中）：**装运未在承诺日期交付。子类型：天气、机械故障、运力不足（无司机）、海关扣押、收货人重新安排。最常见的异常类型（约占所有异常的 40%）。解决取决于延误是承运人过错还是不可抗力。
- **Damage (visible):** Noted on POD at delivery. Carrier liability is strong when consignee documents on the delivery receipt. Photograph immediately. Never accept "driver left before we could inspect."
  - **损坏（可见）：**在交付时在 POD 上注明。当收货人在交付收据上记录时，承运人责任很强。立即拍照。绝不接受"司机在我们检查前就离开了"。
- **Damage (concealed):** Discovered after delivery, not noted on POD. Must file concealed damage claim within 5 days of delivery (industry standard, not law). Burden of proof shifts to shipper. Carrier will challenge — you need packaging integrity evidence.
  - **损坏（隐蔽）：**在交付后发现，未在 POD 上注明。必须在交付后 5 天内提交隐蔽损坏索赔（行业标准，非法律）。举证责任转移到托运人。承运人会质疑 — 你需要包装完整性证据。
- **Damage (temperature):** Reefer/temperature-controlled failure. Requires continuous temp recorder data (Sensitech, Emerson). Pre-trip inspection records are critical. Carriers will claim "product was loaded warm."
  - **损坏（温度）：**冷藏/温控故障。需要连续温度记录仪数据（Sensitech、Emerson）。出发前检查记录至关重要。承运人会声称"产品装车时是热的"。
- **Shortage:** Piece count discrepancy at delivery. Count at the tailgate — never sign clean BOL if count is off. Distinguish driver count vs warehouse count conflicts. OS&D (Over, Short & Damage) report required.
  - **短缺：**交付时件数不符。在尾门处清点 — 如果数量不符，绝不签署清洁 BOL。区分司机清点与仓库清点的冲突。需要 OS&D（溢短损）报告。
- **Overage:** More product delivered than on BOL. Often indicates cross-shipment from another consignee. Trace the extra freight — somebody is short.
  - **溢余：**交付的产品多于 BOL 上的数量。通常表示来自另一个收货人的错装。追踪多余的货物 — 有人会少货。
- **Refused delivery:** Consignee rejects. Reasons: damaged, late (perishable window), incorrect product, no PO match, dock scheduling conflict. Carrier is entitled to storage charges and return freight if refusal is not carrier-fault.
  - **拒收交付：**收货人拒绝。原因：损坏、迟到（易腐窗口）、产品错误、无 PO 匹配、码头排程冲突。如果拒收不是承运人过错，承运人有权收取仓储费和回程运费。
- **Misdelivered:** Delivered to wrong address or wrong consignee. Full carrier liability. Time-critical to recover — product deteriorates or gets consumed.
  - **错投：**交付到错误地址或错误收货人。完全由承运人承担责任。及时追回至关重要 — 产品会变质或被消耗。
- **Lost (full shipment):** No delivery, no scan activity. Trigger trace at 24 hours past ETA for FTL, 48 hours for LTL. File formal tracer with carrier OS&D department.
  - **丢失（整票装运）：**无交付，无扫描活动。FTL 在 ETA 后 24 小时触发追踪，LTL 在 48 小时触发。向承运人 OS&D 部门提交正式追踪单。
- **Lost (partial):** Some items missing from shipment. Often happens at LTL terminals during cross-dock handling. Serial number tracking critical for high-value.
  - **丢失（部分）：**装运中缺少一些物品。常发生在零担终端的交叉转运处理中。序列号追踪对高价值物品至关重要。
- **Contaminated:** Product exposed to chemicals, odors, or incompatible freight (common in LTL). Regulatory implications for food and pharma.
  - **污染：**产品暴露于化学品、异味或不相容的货物中（常见于零担）。对食品和制药有监管影响。

### Carrier Behaviour by Mode

### Carrier Behaviour by Mode
### 按运输模式的承运人行为

Understanding how different carrier types operate changes your resolution strategy:
了解不同承运人类型的运作方式会改变你的解决策略：

- **LTL carriers** (FedEx Freight, XPO, Estes): Shipments touch 2-4 terminals. Each touch = damage risk. Claims departments are large and process-driven. Expect 30-60 day claim resolution. Terminal managers have authority up to ~$2,500.
  - **零担承运人**（FedEx Freight、XPO、Estes）：装运经过 2-4 个终端。每次接触 = 损坏风险。索赔部门规模大，流程驱动。预计 30-60 天解决索赔。终端经理的权限最高约 $2,500。
- **FTL/truckload** (asset carriers + brokers): Single-driver, dock-to-dock. Damage is usually loading/unloading. Brokers add a layer — the broker's carrier may go dark. Always get the actual carrier's MC number.
  - **整车/卡车运输**（资产承运人 + 经纪人）：单一司机，门到门。损坏通常发生在装卸过程中。经纪人增加了一层 — 经纪人的承运人可能会消失。务必获取实际承运人的 MC 编号。
- **Parcel** (UPS, FedEx, USPS): Automated claims portals. Strict documentation requirements. Declared value matters — default liability is very low ($100 for UPS). Must purchase additional coverage at shipping.
  - **包裹**（UPS、FedEx、USPS）：自动索赔门户。严格的文档要求。申报价值很重要 — 默认责任非常低（UPS 为 $100）。必须在发货时购买额外保险。
- **Intermodal** (rail + drayage): Multiple handoffs. Damage often occurs during rail transit (impact events) or chassis swap. Bill of lading chain determines liability allocation between rail and dray.
  - **多式联运**（铁路 + 短途运输）：多次交接。损坏通常发生在铁路运输（冲击事件）或底盘更换期间。提单链决定铁路和短途运输之间的责任分配。
- **Ocean** (container shipping): Governed by Hague-Visby or COGSA (US). Carrier liability is per-package ($500 per package under COGSA unless declared). Container seal integrity is everything. Surveyor inspection at destination port.
  - **海运**（集装箱运输）：受海牙-维斯比规则或 COGSA（美国）约束。承运人责任按件计算（COGSA 下每件 $500，除非申报）。集装箱封条完整性至关重要。目的港需要检验师检查。
- **Air freight:** Governed by Montreal Convention. Strict 14-day notice for damage, 21 days for delay. Weight-based liability limits unless value declared. Fastest claims resolution of all modes.
  - **空运：**受蒙特利尔公约约束。损坏需在 14 天内通知，延误需在 21 天内通知。除非申报价值，否则按重量计算责任限额。所有运输模式中索赔解决最快。

### Claims Process Fundamentals

### Claims Process Fundamentals
### 索赔流程基础知识

- **Carmack Amendment (US domestic surface):** Carrier is liable for actual loss or damage with limited exceptions (act of God, act of public enemy, act of shipper, public authority, inherent vice). Shipper must prove: goods were in good condition when tendered, goods arrived damaged/short, and the amount of damages.
  - **Carmack 修正案（美国国内地面运输）：**承运人对实际丢失或损坏承担责任，有有限例外（天灾、公敌行为、托运人行为、公共当局行为、固有缺陷）。托运人必须证明：货物在交付时处于良好状态，货物到达时损坏/短缺，以及损害金额。
- **Filing deadline:** 9 months from delivery date for US domestic (49 USC § 14706). Miss this and the claim is time-barred regardless of merit.
  - **提交截止日期：**美国国内运输从交付日期起 9 个月（49 USC § 14706）。错过这个期限，无论索赔的优劣如何，都会被时效阻止。
- **Documentation required:** Original BOL (showing clean tender), delivery receipt (showing exception), commercial invoice (proving value), inspection report, photographs, repair estimates or replacement quotes, packaging specifications.
  - **所需文件：**原始 BOL（显示清洁交付）、交付收据（显示异常）、商业发票（证明价值）、检验报告、照片、维修估计或更换报价、包装规格。
- **Carrier response:** Carrier has 30 days to acknowledge, 120 days to pay or decline. If they decline, you have 2 years from the decline date to file suit.
  - **承运人回复：**承运人有 30 天时间确认，120 天时间付款或拒绝。如果他们拒绝，你可以在拒绝日期起 2 年内提起诉讼。

### Seasonal and Cyclical Patterns

### Seasonal and Cyclical Patterns
### 季节性和周期性模式

- **Peak season (Oct-Jan):** Exception rates increase 30-50%. Carrier networks are strained. Transit times extend. Claims departments slow down. Build buffer into commitments.
  - **旺季（10 月-1 月）：**异常率增加 30-50%。承运人网络紧张。运输时间延长。索赔部门放缓。在承诺中加入缓冲。
- **Produce season (Apr-Sep):** Temperature exceptions spike. Reefer availability tightens. Pre-cooling compliance becomes critical.
  - **农产品季节（4 月-9 月）：**温度异常激增。冷藏箱可用性收紧。预冷合规变得至关重要。
- **Hurricane season (Jun-Nov):** Gulf and East Coast disruptions. Force majeure claims increase. Rerouting decisions needed within 4-6 hours of storm track updates.
  - **飓风季节（6 月-11 月）：**墨西哥湾和东海岸中断。不可抗力索赔增加。需要在风暴路径更新后 4-6 小时内做出重新路由决策。
- **Month/quarter end:** Shippers rush volume. Carrier tender rejections spike. Double-brokering increases. Quality suffers across the board.
  - **月末/季度末：**托运人争相发货。承运人接受托运被拒绝的情况激增。双重经纪增加。整体质量下降。
- **Driver shortage cycles:** Worst in Q4 and after new regulation implementation (ELD mandate, FMCSA drug clearinghouse). Spot rates spike, service drops.
  - **司机短缺周期：**在第四季度和新法规实施后最严重（ELD 强制令、FMCSA 药物信息库）。现货价格飙升，服务下降。

### Fraud and Red Flags

### Fraud and Red Flags
### 欺诈和红旗

- **Staged damages:** Damage patterns inconsistent with transit mode. Multiple claims from same consignee location.
  - **蓄意损坏：**损坏模式与运输模式不一致。来自同一收货人地点的多次索赔。
- **Address manipulation:** Redirect requests post-pickup to different addresses. Common in high-value electronics.
  - **地址操纵：**在取货后请求重定向到不同地址。常见于高价值电子产品。
- **Systematic shortages:** Consistent 1-2 unit shortages across multiple shipments — indicates pilferage at a terminal or during transit.
  - **系统性短缺：**多次装运中持续短缺 1-2 件 — 表明在终端或运输中被盗。
- **Double-brokering indicators:** Carrier on BOL doesn't match truck that shows up. Driver can't name their dispatcher. Insurance certificate is from a different entity.
  - **双重经纪指标：**B O L 上的承运人与出现的卡车不符。司机说不出他们调度员的名字。保险证书来自不同的实体。

## Decision Frameworks

## Decision Frameworks
## 决策框架

### Severity Classification

### Severity Classification
### 严重程度分类

Assess every exception on three axes and take the highest severity:
从三个维度评估每个异常，并采用最高严重程度：

**Financial Impact:**
**财务影响：**
- Level 1 (Low): < $1,000 product value, no expedite needed
  - 级别 1（低）：产品价值 < $1,000，无需加急
- Level 2 (Moderate): $1,000 - $5,000 or minor expedite costs
  - 级别 2（中等）：$1,000 - $5,000 或少量加急费用
- Level 3 (Significant): $5,000 - $25,000 or customer penalty risk
  - 级别 3（重大）：$5,000 - $25,000 或客户罚款风险
- Level 4 (Major): $25,000 - $100,000 or contract compliance risk
  - 级别 4（主要）：$25,000 - $100,000 或合同合规风险
- Level 5 (Critical): > $100,000 or regulatory/safety implications
  - 级别 5（严重）：> $100,000 或监管/安全影响

**Customer Impact:**
**客户影响：**
- Standard customer, no SLA at risk → does not elevate
  - 标准客户，SLA 无风险 → 不升级
- Key account with SLA at risk → elevate by 1 level
  - 关键客户，SLA 有风险 → 升级 1 级
- Enterprise customer with penalty clauses → elevate by 2 levels
  - 有罚款条款的企业客户 → 升级 2 级
- Customer's production line or retail launch at risk → automatic Level 4+
  - 客户生产线或零售发布有风险 → 自动 4 级以上

**Time Sensitivity:**
**时间敏感性：**
- Standard transit with buffer → does not elevate
  - 有缓冲的标准运输 → 不升级
- Delivery needed within 48 hours, no alternative sourced → elevate by 1
  - 需要在 48 小时内交付，没有替代货源 → 升级 1 级
- Same-day or next-day critical (production shutdown, event deadline) → automatic Level 4+
  - 当天或次日关键（生产线停工、活动截止日期）→ 自动 4 级以上

### Eat-the-Cost vs Fight-the-Claim

### Eat-the-Cost vs Fight-the-Claim
### 承担成本还是争取索赔

This is the most common judgment call. Thresholds:
这是最常见的判断。阈值：

- **< $500 and carrier relationship is strong:** Absorb. The admin cost of claims processing ($150-250 internal) makes it negative-ROI. Log for carrier scorecard.
  - **< $500 且承运人关系良好：**承担。索赔处理的行政成本（内部 $150-250）使其 ROI 为负。记录在承运人记分卡中。
- **$500 - $2,500:** File claim but don't escalate aggressively. This is the "standard process" zone. Accept partial settlements above 70% of value.
  - **$500 - $2,500：**提交索赔但不积极升级。这是"标准流程"区域。接受高于价值 70% 的部分和解。
- **$2,500 - $10,000:** Full claims process. Escalate at 30-day mark if no resolution. Involve carrier account manager. Reject settlements below 80%.
  - **$2,500 - $10,000：**完整索赔流程。如果 30 天内无解决方案则升级。涉及承运人客户经理。拒绝低于 80% 的和解。
- **> $10,000:** VP-level awareness. Dedicated claims handler. Independent inspection if damage. Reject settlements below 90%. Legal review if denied.
  - **> $10,000：**VP 级别知晓。专职索赔处理员。如有损坏则进行独立检查。拒绝低于 90% 的和解。如果被拒绝则进行法律审查。
- **Any amount + pattern:** If this is the 3rd+ exception from the same carrier in 30 days, treat it as a carrier performance issue regardless of individual dollar amounts.
  - **任何金额 + 模式：**如果这是同一承运人 30 天内的第 3 个或更多异常，无论个别美元金额如何，都将其视为承运人绩效问题。

### Priority Sequencing

### Priority Sequencing
### 优先级排序

When multiple exceptions are active simultaneously (common during peak season or weather events), prioritize:
当多个异常同时活跃时（在旺季或天气事件期间很常见），优先处理：

1. Safety/regulatory (temperature-controlled pharma, hazmat) — always first
   - 安全/监管（温控药品、危险品）— 始终优先
2. Customer production shutdown risk — financial multiplier is 10-50x product value
   - 客户生产线停工风险 — 财务乘数是产品价值的 10-50 倍
3. Perishable with remaining shelf life < 48 hours
   - 剩余保质期 < 48 小时的易腐物品
4. Highest financial impact adjusted for customer tier
   - 根据客户层级调整的最高财务影响
5. Oldest unresolved exception (prevent aging beyond SLA)
   - 最旧的未解决异常（防止超过 SLA 的老化）

## Key Edge Cases

## Key Edge Cases
## 关键边缘情况

These are situations where the obvious approach is wrong. Brief summaries are included here so you can expand them into project-specific playbooks if needed.
这些是明显方法错误的情况。这里包含简要摘要，以便你可以在需要时将它们扩展为项目特定的 playbook。

1. **Pharma reefer failure with disputed temps:** Carrier shows correct set-point; your Sensitech data shows excursion. The dispute is about sensor placement and pre-cooling. Never accept carrier's single-point reading — demand continuous data logger download.
   - **药品冷藏故障与有争议的温度：**承运人显示正确的设定点；你的 Sensitech 数据显示偏离。争议在于传感器放置和预冷。绝不接受承运人的单点读数 — 要求下载连续数据记录仪数据。

2. **Consignee claims damage but caused it during unloading:** POD is signed clean, but consignee calls 2 hours later claiming damage. If your driver witnessed their forklift drop the pallet, the driver's contemporaneous notes are your best defense. Without that, concealed damage claim against you is likely.
   - **收货人声称损坏但是在卸货时造成的：**POD 签署为清洁，但收货人 2 小时后打电话声称损坏。如果你的司机目睹了他们叉车掉落托盘，司机当时的记录是你最好的防御。否则，针对你的隐蔽损坏索赔很可能成立。

3. **72-hour scan gap on high-value shipment:** No tracking updates doesn't always mean lost. LTL scan gaps happen at busy terminals. Before triggering a loss protocol, call the origin and destination terminals directly. Ask for physical trailer/bay location.
   - **高价值装运的 72 小时扫描间隔：**没有跟踪更新并不总是意味着丢失。零担扫描间隔发生在繁忙的终端。在触发丢失协议之前，直接致电原产地和目的地终端。询问实际的拖车/泊位位置。

4. **Cross-border customs hold:** When a shipment is held at customs, determine quickly if the hold is for documentation (fixable) or compliance (potentially unfixable). Carrier documentation errors (wrong harmonized codes on the carrier's portion) vs shipper errors (incorrect commercial invoice values) require different resolution paths.
   - **跨境海关扣押：**当装运被海关扣押时，快速确定扣押是为了文件（可修复）还是合规（可能无法修复）。承运人文件错误（承运人部分的协调编码错误）与托运人错误（商业发票价值不正确）需要不同的解决路径。

5. **Partial deliveries against single BOL:** Multiple delivery attempts where quantities don't match. Maintain a running tally. Don't file shortage claim until all partials are reconciled — carriers will use premature claims as evidence of shipper error.
   - **针对单一 BOL 的分批交付：**数量不匹配的多重交付尝试。保持累计记录。在所有分批交付协调之前不要提交短缺索赔 — 承运人会使用过早的索赔作为托运人错误的证据。

6. **Broker insolvency mid-shipment:** Your freight is on a truck, the broker who arranged it goes bankrupt. The actual carrier has a lien right. Determine quickly: is the carrier paid? If not, negotiate directly with the carrier for release.
   - **经纪人在运输途中破产：**你的货物在卡车上，安排运输的经纪人破产了。实际承运人有留置权。快速确定：承运人是否已付款？如果没有，直接与承运人谈判以释放货物。

7. **Concealed damage discovered at final customer:** You delivered to distributor, distributor delivered to end customer, end customer finds damage. The chain-of-custody documentation determines who bears the loss.
   - **在最终客户处发现隐蔽损坏：**你交付给经销商，经销商交付给最终客户，最终客户发现损坏。保管链文档决定谁承担损失。

8. **Peak surcharge dispute during weather event:** Carrier applies emergency surcharge retroactively. Contract may or may not allow this — check force majeure and fuel surcharge clauses specifically.
   - **天气事件期间的旺季附加费争议：**承运人追溯应用紧急附加费。合同可能允许也可能不允许 — 特别检查不可抗力和燃油附加费条款。

## Communication Patterns

## Communication Patterns
## 沟通模式

### Tone Calibration

### Tone Calibration
### 语气校准

Match communication tone to situation severity and relationship:
根据情况严重程度和关系匹配沟通语气：

- **Routine exception, good carrier relationship:** Collaborative. "We've got a delay on PRO# X — can you get me an updated ETA? Customer is asking."
  - **常规异常，良好的承运人关系：**协作型。"我们在 PRO# X 上有延误 — 你能给我一个更新的 ETA 吗？客户在问。"
- **Significant exception, neutral relationship:** Professional and documented. State facts, reference BOL/PRO, specify what you need and by when.
  - **重大异常，中性关系：**专业且有据可查。陈述事实，参考 BOL/PRO，明确你需要什么以及何时需要。
- **Major exception or pattern, strained relationship:** Formal. CC management. Reference contract terms. Set response deadlines. "Per Section 4.2 of our transportation agreement dated..."
  - **重大异常或模式，紧张关系：**正式。抄送管理层。参考合同条款。设定回复截止日期。"根据我们运输协议第 4.2 条，日期为..."
- **Customer-facing (delay):** Proactive, honest, solution-oriented. Never blame the carrier by name. "Your shipment has experienced a transit delay. Here's what we're doing and your updated timeline."
  - **面向客户（延误）：**主动、诚实、解决方案导向。绝不点名责备承运人。"您的装运遇到了运输延误。这是我们正在做的事情和您更新的时间表。"
- **Customer-facing (damage/loss):** Empathetic, action-oriented. Lead with the resolution, not the problem. "We've identified an issue with your shipment and have already initiated [replacement/credit]."
  - **面向客户（损坏/丢失）：**同理心强、以行动为导向。首先说明解决方案，而不是问题。"我们发现了您的装运问题，并已启动 [替换/信用]。"

### Key Templates

### Key Templates
### 关键模板

Brief templates appear below. Adapt them to your carrier, customer, and insurance workflows before using them in production.
以下提供简要模板。在生产中使用之前，根据你的承运人、客户和保险工作流程进行调整。

**Initial carrier inquiry:** Subject: `Exception Notice — PRO# {pro} / BOL# {bol}`. State: what happened, what you need (ETA update, inspection, OS&D report), and by when.
**初始承运人查询：**主题：`异常通知 — PRO# {pro} / BOL# {bol}`。说明：发生了什么，你需要什么（ETA 更新、检查、OS&D 报告），以及何时需要。

**Customer proactive update:** Lead with: what you know, what you're doing about it, what the customer's revised timeline is, and your direct contact for questions.
**客户主动更新：**首先说明：你知道什么，你正在做什么，客户的修订时间表是什么，以及你的直接联系方式。

**Escalation to carrier management:** Subject: `ESCALATION: Unresolved Exception — {shipment_ref} — {days} Days`. Include timeline of previous communications, financial impact, and what resolution you expect.
**升级到承运人管理层：**主题：`升级：未解决的异常 — {shipment_ref} — {days} 天`。包括先前沟通的时间线、财务影响，以及你期望的解决方案。

## Escalation Protocols

## Escalation Protocols
## 升级协议

### Automatic Escalation Triggers

### Automatic Escalation Triggers
### 自动升级触发器

| Trigger | Action | Timeline | Trigger zh-CN | Action zh-CN | Timeline zh-CN |
|---|---|---|---|---|---|
| Exception value > $25,000 | Notify VP Supply Chain immediately | Within 1 hour | 异常值 > $25,000 | 立即通知供应链 VP | 1 小时内 |
| Enterprise customer affected | Assign dedicated handler, notify account team | Within 2 hours | 影响企业客户 | 指派专职处理员，通知客户团队 | 2 小时内 |
| Carrier non-response | Escalate to carrier account manager | After 4 hours | 承运人无响应 | 升级到承运人客户经理 | 4 小时后 |
| Repeated carrier (3+ in 30 days) | Carrier performance review with procurement | Within 1 week | 承运人重复（30 天内 3+ 次） | 与采购进行承运人绩效审查 | 1 周内 |
| Potential fraud indicators | Notify compliance and halt standard processing | Immediately | 潜在欺诈指标 | 通知合规部门并停止标准处理 | 立即 |
| Temperature excursion on regulated product | Notify quality/regulatory team | Within 30 minutes | 受监管产品的温度偏离 | 通知质量/监管团队 | 30 分钟内 |
| No scan update on high-value (> $50K) | Initiate trace protocol and notify security | After 24 hours | 高价值（> $50K）无扫描更新 | 启动追踪协议并通知安全部门 | 24 小时后 |
| Claims denied > $10,000 | Legal review of denial basis | Within 48 hours | 索赔被拒绝 > $10,000 | 法律审查拒绝理由 | 48 小时内 |

### Escalation Chain

### Escalation Chain
### 升级链

Level 1 (Analyst) → Level 2 (Team Lead, 4 hours) → Level 3 (Manager, 24 hours) → Level 4 (Director, 48 hours) → Level 5 (VP, 72+ hours or any Level 5 severity)
级别 1（分析师）→ 级别 2（团队负责人，4 小时）→ 级别 3（经理，24 小时）→ 级别 4（总监，48 小时）→ 级别 5（VP，72+ 小时或任何 5 级严重程度）

## Performance Indicators

## Performance Indicators
## 绩效指标

Track these metrics weekly and trend monthly:
每周跟踪这些指标，每月趋势分析：

| Metric | Target | Red Flag | Metric zh-CN | Target zh-CN | Red Flag zh-CN |
|---|---|---|---|---|---|
| Mean resolution time | < 72 hours | > 120 hours | 平均解决时间 | < 72 小时 | > 120 小时 |
| First-contact resolution rate | > 40% | < 25% | 首次接触解决率 | > 40% | < 25% |
| Financial recovery rate (claims) | > 75% | < 50% | 财务追偿率（索赔） | > 75% | < 50% |
| Customer satisfaction (post-exception) | > 4.0/5.0 | < 3.5/5.0 | 客户满意度（异常后） | > 4.0/5.0 | < 3.5/5.0 |
| Exception rate (per 1,000 shipments) | < 25 | > 40 | 异常率（每 1,000 票装运） | < 25 | > 40 |
| Claims filing timeliness | 100% within 30 days | Any > 60 days | 索赔提交及时性 | 30 天内 100% | 任何 > 60 天 |
| Repeat exceptions (same carrier/lane) | < 10% | > 20% | 重复异常（相同承运人/航线） | < 10% | > 20% |
| Aged exceptions (> 30 days open) | < 5% of total | > 15% | 老旧异常（> 30 天未结） | < 总数的 5% | > 15% |

## Additional Resources

## Additional Resources
## 额外资源

- Pair this skill with your internal claims deadlines, mode-specific escalation matrix, and insurer notice requirements.
  - 将此技能与你的内部索赔截止日期、特定模式升级矩阵和保险公司通知要求配对使用。
- Keep carrier-specific proof-of-delivery rules and OS&D checklists near the team that will execute the playbooks.
  - 将特定承运人的交付证明规则和 OS&D 清单放在将执行 playbook 的团队附近。
