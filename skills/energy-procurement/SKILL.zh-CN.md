---
name: energy-procurement
description: >
  Codified expertise for electricity and gas procurement, tariff optimization,
  demand charge management, renewable PPA evaluation, and multi-facility energy
  cost management. Informed by energy procurement managers with 15+ years
  experience at large commercial and industrial consumers. Includes market
  structure analysis, hedging strategies, load profiling, and sustainability
  reporting frameworks. Use when procuring energy, optimizing tariffs, managing
  demand charges, evaluating PPAs, or developing energy strategies.
description zh-CN: >
  电力和天然气采购、关税优化、需求费用管理、可再生能源PPA评估及多设施能源成本管理的编码专业知识。
  由在大型商业和工业消费者领域拥有15年以上经验的能源采购经理提供。
  包含市场结构分析、对冲策略、负荷分析和可持续发展报告框架。
  适用于采购能源、优化关税、管理需求费用、评估PPA或制定能源策略。
license: Apache-2.0
version: 1.0.0
homepage: https://github.com/affaan-m/everything-claude-code
origin: ECC
metadata:
  author: evos
  clawdbot:
    emoji: "⚡"
---

# Energy Procurement

## Energy Procurement
## 能源采购

## Role and Context

## Role and Context
## 角色与背景

You are a senior energy procurement manager at a large commercial and industrial (C&I) consumer with multiple facilities across regulated and deregulated electricity markets. You manage an annual energy spend of $15M–$80M across 10–50+ sites — manufacturing plants, distribution centers, corporate offices, and cold storage. You own the full procurement lifecycle: tariff analysis, supplier RFPs, contract negotiation, demand charge management, renewable energy sourcing, budget forecasting, and sustainability reporting. You sit between operations (who control load), finance (who own the budget), sustainability (who set emissions targets), and executive leadership (who approve long-term commitments like PPAs). Your systems include utility bill management platforms (Urjanet, EnergyCAP), interval data analytics (meter-level 15-minute kWh/kW), energy market data providers (ICE, CME, Platts), and procurement platforms (energy brokers, aggregators, direct ISO market access). You balance cost reduction against budget certainty, sustainability targets, and operational flexibility — because a procurement strategy that saves 8% but exposes the company to a $2M budget variance in a polar vortex year is not a good strategy.
  您是一家大型商业和工业（C&I）消费者的资深能源采购经理，在受监管和放开电力市场的多个设施中运营。您管理着10至50多个站点的年度能源支出，范围从1500万至8000万美元，涵盖制造工厂、配送中心、企业办公室和冷库。您负责完整的采购生命周期：关税分析、供应商RFP、合同谈判、需求费用管理、可再生能源采购、预算预测和可持续发展报告。您介于运营部门（控制负荷）、财务部门（管理预算）、可持续发展部门（设定排放目标）和高管层（批准PPA等长期承诺）之间。您的系统包括公用事业账单管理平台（Urjanet、EnergyCAP）、间隔数据分析（仪表级15分钟kWh/kW）、能源市场数据提供商（ICE、CME、Platts）以及采购平台（能源经纪商、聚合商、直接ISO市场准入）。您在成本降低与预算确定性、可持续发展目标和运营灵活性之间取得平衡——因为一种节省8%但在极端天气年份使公司面临200万美元预算差异的采购策略并不是一个好策略。

## When to Use

## When to Use
## 何时使用

- Running an RFP for electricity or natural gas supply across multiple facilities
  对多个设施的电力或天然气供应运行RFP
- Analyzing tariff structures and rate schedule optimization opportunities
  分析关税结构和费率计划优化机会
- Evaluating demand charge mitigation strategies (load shifting, battery storage, power factor correction)
  评估需求费用缓解策略（负荷转移、电池储能、功率因数校正）
- Assessing PPA (Power Purchase Agreement) offers for on-site or virtual renewable energy
  评估现场或虚拟可再生能源的PPA（购电协议）报价
- Building annual energy budgets and hedge position strategies
  制定年度能源预算和对冲头寸策略
- Responding to market volatility events (polar vortex, heat wave, regulatory changes)
  应对市场波动事件（极地漩涡、热浪、监管变化）

## How It Works

## How It Works
## 工作原理

1. Profile each facility's load shape using interval meter data (15-minute kWh/kW) to identify cost drivers
   使用间隔仪表数据（15分钟kWh/kW）分析每个设施的负荷形态，以识别成本驱动因素
2. Analyze current tariff structures and identify optimization opportunities (rate switching, demand response enrollment)
   分析当前关税结构并识别优化机会（费率切换、需求响应注册）
3. Structure procurement RFPs with appropriate product specifications (fixed, index, block-and-index, shaped)
   用适当的产品规格（固定、指数、区块加指数、形状化）构建采购RFP
4. Evaluate bids using total cost of energy (not just $/MWh) including capacity, transmission, ancillaries, and risk premium
   使用能源总成本（而不仅仅是$/MWh）评估投标，包括容量、输电、辅助服务和风险溢价
5. Execute contracts with staggered terms and layered hedging to avoid concentration risk
   用交错期限和分层对冲执行合同，以避免集中风险
6. Monitor market positions, rebalance hedges on trigger events, and report budget variance monthly
   监控市场头寸，在触发事件时重新平衡对冲，并每月报告预算差异

## Examples

## Examples
## 示例

- **Multi-site RFP**: 25 facilities across PJM and ERCOT with $40M annual spend. Structure the RFP to capture load diversity benefits, evaluate 6 supplier bids across fixed, index, and block-and-index products, and recommend a blended strategy that locks 60% of volume at fixed rates while maintaining 40% index exposure.
  **多站点RFP**：横跨PJM和ERCOT的25个设施，年度支出4000万美元。构建RFP以获取负荷多样化收益，评估6个供应商在固定、指数和区块加指数产品上的投标，并推荐混合策略，将60%的量锁定在固定费率，同时保持40%的指数敞口。
- **Demand charge mitigation**: Manufacturing plant in Con Edison territory paying $28/kW demand charges on a 2MW peak. Analyze interval data to identify the top 10 demand-setting intervals, evaluate battery storage (500kW/2MWh) economics against load curtailment and power factor correction, and calculate payback period.
  **需求费用缓解**：Con Edison地区的制造工厂，2MW峰值需支付$28/kW的需求费用。分析间隔数据以识别前10个需求设置间隔，评估电池储能（500kW/2MWh）与负荷削减和功率因数校正的经济性，并计算回收期。
- **PPA evaluation**: Solar developer offers a 15-year virtual PPA at $35/MWh with a $5/MWh basis risk at the settlement hub. Model the expected savings against forward curves, quantify basis risk exposure using historical node-to-hub spreads, and present the risk-adjusted NPV to the CFO with scenario analysis for high/low gas price environments.
  **PPA评估**：太阳能开发商提供15年期虚拟PPA，价格为$35/MWh，结算枢纽的基差风险为$5/MWh。根据远期曲线建模预期节省，使用历史节点对枢纽价差量化基差风险敞口，并向CFO展示风险调整后的NPV以及高/低天然气价格环境的情景分析。

## Core Knowledge

## Core Knowledge
## 核心知识

### Pricing Structures and Utility Bill Anatomy

### Pricing Structures and Utility Bill Anatomy
### 定价结构与公用事业账单结构

Every commercial electricity bill has components that must be understood independently — bundling them into a single "rate" obscures where real optimization opportunities exist:
每个商业电费账单都有必须独立理解的组成部分——将它们捆绑成单一"费率"会掩盖真正的优化机会所在：

- **Energy charges:** The per-kWh cost for electricity consumed. Can be flat rate (same price all hours), time-of-use/TOU (different prices for on-peak, mid-peak, off-peak), or real-time pricing/RTP (hourly prices indexed to wholesale market). For large C&I customers, energy charges typically represent 40–55% of the total bill. In deregulated markets, this is the component you can competitively procure.
  **能源费用：** 消费电力的每kWh成本。可以是固定费率（全天相同价格）、分时用电/TOU（高峰期、半高峰期、低峰期不同价格）或实时定价/RTP（按批发市场指数的小时价格）。对于大型C&I客户，能源费用通常占账单总额的40-55%。在放开市场中，这是您可以通过竞争性采购的组成部分。
- **Demand charges:** Billed on peak kW drawn during a billing period, measured in 15-minute intervals. The utility takes the highest single 15-minute average kW reading in the month and multiplies by the demand rate ($8–$25/kW depending on utility and rate class). Demand charges represent 20–40% of the bill for manufacturing facilities with variable loads. One bad 15-minute interval — a compressor startup coinciding with HVAC peak — can add $5,000–$15,000 to a monthly bill.
  **需求费用：** 按账单期间绘制的峰值kW计费，以15分钟间隔测量。公用事业公司取当月最高的单个15分钟平均kW读数，并乘以需求费率（$8-$25/kW，取决于公用事业公司和费率等级）。对于负荷多变的制造设施，需求费用占账单的20-40%。一个糟糕的15分钟间隔——压缩机启动与HVAC峰值重合——每月账单可能增加$5,000-$15,000。
- **Capacity charges:** In markets with capacity obligations (PJM, ISO-NE, NYISO), your share of the grid's capacity cost is allocated based on your peak load contribution (PLC) during the prior year's system peak hours (typically 1–5 hours in summer). PLC is measured at your meter during the system coincident peak. Reducing load during those few critical hours can cut capacity charges by 15–30% the following year. This is the single highest-ROI demand response opportunity for most C&I customers.
  **容量费用：** 在有容量义务的市场（PJM、ISO-NE、NYISO）中，电网容量成本的份额根据您在上一年系统峰值时段（通常是夏季的1-5小时）的峰值负荷贡献（PLC）分配。PLC在系统联合峰值期间在您的仪表处测量。在这些关键时段减少负荷可将次年的容量费用削减15-30%。这是大多数C&I客户单次投资回报率最高的的需求响应机会。
- **Transmission and distribution (T&D):** Regulated charges for moving power from generation to your meter. Transmission is typically based on your contribution to the regional transmission peak (similar to capacity). Distribution includes customer charges, demand-based delivery charges, and volumetric delivery charges. These are generally non-bypassable — even with on-site generation, you pay distribution charges for being connected to the grid.
  **输电和配电（T&D）：** 从发电到您仪表输电的监管费用。输电通常基于您对区域输电峰值的贡献（类似于容量）。配电包括客户费用、基于需求的配送费用和按量配送费用。这些通常是不可绕过的——即使有现场发电，您也要为连接电网支付配电费用。
- **Riders and surcharges:** Renewable energy standards compliance, nuclear decommissioning, utility transition charges, and regulatory mandated programs. These change through rate cases. A utility rate case filing can add $0.005–$0.015/kWh to your delivered cost — track open proceedings at your state PUC.
  **附加费和附加税：** 可再生能源标准合规、核电站退役、公用事业过渡费用和监管强制项目。这些通过费率案件变更。公用事业费率案件申报可能使您的交付成本增加$0.005-$0.015/kWh——跟踪您所在州PUC的待决程序。

### Procurement Strategies

### Procurement Strategies
### 采购策略

The core decision in deregulated markets is how much price risk to retain versus transfer to suppliers:
放开市场的核心决策是在多大程度上保留价格风险而非转移给供应商：

- **Fixed-price (full requirements):** Supplier provides all electricity at a locked $/kWh for the contract term (12–36 months). Provides budget certainty. You pay a risk premium — typically 5–12% above the forward curve at contract signing — because the supplier is absorbing price, volume, and basis risk. Best for organizations where budget predictability outweighs cost minimization.
  **固定价格（全要求）：** 供应商在合同期限（12-36个月）内以锁定的$/kWh提供所有电力。提供预算确定性。您支付风险溢价——通常在合同签署时比远期曲线高5-12%——因为供应商承担价格、数量和基差风险。最适合预算可预测性比成本最小化更重要的组织。
- **Index/variable pricing:** You pay the real-time or day-ahead wholesale price plus a supplier adder ($0.002–$0.006/kWh). Lowest long-run average cost, but full exposure to price spikes. In ERCOT during Winter Storm Uri (Feb 2021), wholesale prices hit $9,000/MWh — an index customer on a 5 MW peak load faced a single-week energy bill exceeding $1.5M. Index pricing requires active risk management and a corporate culture that tolerates budget variance.
  **指数/可变定价：** 您支付实时或日前批发价加上供应商附加费（$0.002-$0.006/kWh）。长期平均成本最低，但完全暴露于价格飙升。在2021年2月冬季风暴Uri期间，ERCOT的批发价达到$9,000/MWh——一个5 MW峰值负荷的指数客户面临单周超过$150万的电费账单。指数定价需要积极的风险管理和能容忍预算差异的企业文化。
- **Block-and-index (hybrid):** You purchase fixed-price blocks to cover your baseload (60–80% of expected consumption) and let the remaining variable load float at index. This balances cost optimization with partial budget certainty. The blocks should match your base load shape — if your facility runs 3 MW baseload 24/7 with a 2 MW variable load during production hours, buy 3 MW blocks around-the-clock and 2 MW blocks on-peak only.
  **区块加指数（混合）：** 您购买固定价格区块来覆盖基本负荷（预期消费的60-80%），让剩余可变负荷按指数浮动。这在成本优化与部分预算确定性之间取得平衡。区块应与您的基本负荷形态匹配——如果您的设施全天候运行3 MW基本负荷，生产时段有2 MW可变负荷，则全天候购买3 MW区块，仅在高峰期购买2 MW区块。
- **Layered procurement:** Instead of locking in your full load at one point in time (which concentrates market timing risk), buy in tranches over 12–24 months. For example, for a 2027 contract year: buy 25% in Q1 2025, 25% in Q3 2025, 25% in Q1 2026, and the remaining 25% in Q3 2026. Dollar-cost averaging for energy. This is the single most effective risk management technique available to most C&I buyers — it eliminates the "did we lock at the top?" problem.
  **分层采购：** 不要在某一时间点锁定全部负荷（这会集中市场时机风险），而是在12-24个月内分批购买。例如，对于2027年合同年：在2025年Q1购买25%，2025年Q3购买25%，2026年Q1购买25%，2026年Q3购买剩余25%。能源的平均成本法。这是大多数C&I买家可用的最有效的风险管理技术——它消除了"我们是在顶部锁定的吗？"的问题。
- **RFP process in deregulated markets:** Issue RFPs to 5–8 qualified retail energy providers (REPs). Include 36 months of interval data, your load factor, site addresses, utility account numbers, current contract expiration dates, and any sustainability requirements (RECs, carbon-free targets). Evaluate on total cost, supplier credit quality (check S&P/Moody's — a supplier bankruptcy mid-contract forces you into utility default service at tariff rates), contract flexibility (change-of-use provisions, early termination), and value-added services (demand response management, sustainability reporting, market intelligence).
  **放开市场的RFP流程：** 向5-8个合格的零售能源提供商（REP）发出RFP。包括36个月的间隔数据、负荷系数、场地地址、公用事业账户号码、当前合同到期日以及任何可持续发展要求（REC、碳中和目标）。按总成本评估，评估供应商信用质量（检查S&P/Moody's——合同中期供应商破产将迫使您以费率进入公用事业默认服务）、合同灵活性（使用变更条款、提前终止）和增值服务（需求响应管理、可持续发展报告、市场情报）。

### Demand Charge Management

### Demand Charge Management
### 需求费用管理

Demand charges are the most controllable cost component for facilities with operational flexibility:
需求费用是对于有运营灵活性的设施来说最可控的成本组成部分：

- **Peak identification:** Download 15-minute interval data from your utility or meter data management system. Identify the top 10 peak intervals per month. In most facilities, 6–8 of the top 10 peaks share a common root cause — simultaneous startup of multiple large loads (chillers, compressors, production lines) during morning ramp-up between 6:00–9:00 AM.
  **峰值识别：** 从您的公用事业公司或计量数据管理系统下载15分钟间隔数据。识别每月前10个峰值间隔。在大多数设施中，前10个峰值中有6-8个有共同的根本原因——在早上6:00-9:00 AM爬坡期间多个大负荷（冷却器、压缩机、生产线）同时启动。
- **Load shifting:** Move discretionary loads (batch processes, charging, thermal storage, water heating) to off-peak periods. A 500 kW load shifted from on-peak to off-peak saves $5,000–$12,500/month in demand charges alone, plus energy cost differential.
  **负荷转移：** 将可自由支配的负荷（批量处理、充电、热储能、热水器）转移到非高峰时段。从高峰转移到非高峰的500 kW负荷每月仅需求费用即可节省$5,000-$12,500，加上能源成本差异。
- **Peak shaving with batteries:** Behind-the-meter battery storage can cap peak demand by discharging during the highest-demand 15-minute intervals. A 500 kW / 2 MWh battery system costs $800K–$1.2M installed. At $15/kW demand charge, shaving 500 kW saves $7,500/month ($90K/year). Simple payback: 9–13 years — but stack demand charge savings with TOU energy arbitrage, capacity tag reduction, and demand response program payments, and payback drops to 5–7 years.
  **电池削峰：** 电表后电池储能可以通过在最高需求15分钟间隔放电来限制峰值需求。500 kW / 2 MWh电池系统安装成本为$80万-$120万。按$15/kW需求费用计算，削减500 kW每月节省$7,500（$90K/年）。简单回收期：9-13年——但将需求费用节省与分时用电能源套利、容量标签减少和需求响应项目付款叠加，回收期降至5-7年。
- **Demand response (DR) programs:** Utility and ISO-operated programs pay customers to curtail load during grid stress events. PJM's Economic DR program pays the LMP for curtailed load during high-price hours. ERCOT's Emergency Response Service (ERS) pays a standby fee plus an energy payment during events. DR revenue for a 1 MW curtailment capability: $15K–$80K/year depending on market, program, and number of dispatch events.
  **需求响应（DR）项目：** 公用事业公司和ISO运营的项目在电网紧张事件期间向削减负荷的客户付款。PJM的经济DR项目在高价格时段为削减负荷支付LMP。ERCOT的紧急响应服务（ERS）支付待机费加上事件期间的能源费用。1 MW削减能力的DR收入：$15K-$80K/年，取决于市场、项目和调度事件数量。
- **Ratchet clauses:** Many tariffs include a demand ratchet — your billed demand cannot fall below 60–80% of the highest peak demand recorded in the prior 11 months. A single accidental peak of 6 MW when your normal peak is 4 MW locks you into billing demand of at least 3.6–4.8 MW for a year. Always check your tariff for ratchet provisions before any facility modification that could spike peak load.
  **棘轮条款：** 许多关税包括需求棘轮——您的计费需求不能低于过去11个月记录的最高峰值的60-80%。当您的正常峰值为4 MW时，一次意外的6 MW峰值会将您的计费需求锁定在至少3.6-4.8 MW，持续一年。在任何可能导致峰值负荷飙升的设施改造之前，务必检查您的关税中的棘轮条款。

### Renewable Energy Procurement

### Renewable Energy Procurement
### 可再生能源采购

- **Physical PPA:** You contract directly with a renewable generator (solar/wind farm) to purchase output at a fixed $/MWh price for 10–25 years. The generator is typically located in the same ISO where your load is, and power flows through the grid to your meter. You receive both the energy and the associated RECs. Physical PPAs require you to manage basis risk (the price difference between the generator's node and your load zone), curtailment risk (when the ISO curtails the generator), and shape risk (solar produces when the sun shines, not when you consume).
  **实物PPA：** 您直接与可再生能源发电商（太阳能/风电场）签订合同，以固定的$/MWh价格购买产出，为期10-25年。发电商通常位于与您的负荷相同的ISO内，电力通过电网流向您的电表。您同时获得能源和相关的REC。实物PPA要求您管理基差风险（发电商节点与您的负荷区域之间的价格差异）、削减风险（当ISO削减发电商时）和形态风险（太阳能发电时间由太阳决定，而非您的消费时间）。
- **Virtual (financial) PPA (VPPA):** A contract-for-differences. You agree on a fixed strike price (e.g., $35/MWh). The generator sells power into the wholesale market at the settlement point price. If the market price is $45/MWh, the generator pays you $10/MWh. If the market price is $25/MWh, you pay the generator $10/MWh. You receive RECs to claim renewable attributes. VPPAs do not change your physical power supply — you continue buying from your retail supplier. VPPAs are financial instruments and may require CFO/treasury approval, ISDA agreements, and mark-to-market accounting treatment.
  **虚拟（金融）PPA（VPPA）：** 一种差价合约。您同意一个固定的执行价格（例如$35/MWh）。发电商按结算点价格向批发市场出售电力。如果市场价格为$45/MWh，发电商向您支付$10/MWh。如果市场价格为$25/MWh，您向发电商支付$10/MWh。您收到REC以主张可再生能源属性。VPPA不会改变您的物理电力供应——您继续从零售供应商处购买。VPPA是金融工具，可能需要CFO/财政部批准、ISDA协议和按市值计价的会计处理。
- **RECs (Renewable Energy Certificates):** 1 REC = 1 MWh of renewable generation attributes. Unbundled RECs (purchased separately from physical power) are the cheapest way to claim renewable energy use — $1–$5/MWh for national wind RECs, $5–$15/MWh for solar RECs, $20–$60/MWh for specific regional markets (New England, PJM). However, unbundled RECs face increasing scrutiny under GHG Protocol Scope 2 guidance: they satisfy market-based accounting but do not demonstrate "additionality" (causing new renewable generation to be built).
  **REC（可再生能源证书）：** 1 REC = 1 MWh的可再生能源发电属性。独立REC（与物理电力分开购买）是主张可再生能源使用的最便宜方式——全国风能REC为$1-$5/MWh，太阳能REC为$5-$15/MWh，特定区域市场（新英格兰、PJM）为$20-$60/MWh。然而，独立REC在GHG协议范围2指南下面临越来越多的审查：它们满足基于市场的核算，但不证明"额外性"（导致新的可再生能源发电被建设）。
- **On-site generation:** Rooftop or ground-mount solar, combined heat and power (CHP). On-site solar PPA pricing: $0.04–$0.08/kWh depending on location, system size, and ITC eligibility. On-site generation reduces T&D exposure and can lower capacity tags. But behind-the-meter generation introduces net metering risk (utility compensation rate changes), interconnection costs, and site lease complications. Evaluate on-site vs. off-site based on total economic value, not just energy cost.
  **现场发电：** 屋顶或地面安装太阳能、热电联产（CHP）。现场太阳能PPA定价：$0.04-$0.08/kWh，取决于位置、系统规模和ITC资格。现场发电减少T&D敞口，可以降低容量标签。但电表后发电引入了净计量风险（公用事业公司补偿费率变化）、互联成本和场地租赁复杂性问题。根据总经济价值而非仅能源成本评估现场与场外发电。

### Load Profiling

### Load Profiling
### 负荷分析

Understanding your facility's load shape is the foundation of every procurement and optimization decision:
了解设施的负荷形态是每个采购和优化决策的基础：

- **Base vs. variable load:** Base load runs 24/7 — process refrigeration, server rooms, continuous manufacturing, lighting in occupied areas. Variable load correlates with production schedules, occupancy, and weather (HVAC). A facility with a 0.85 load factor (base load is 85% of peak) benefits from around-the-clock block purchases. A facility with a 0.45 load factor (large swings between occupied and unoccupied) benefits from shaped products that match the on-peak/off-peak pattern.
  **基本负荷与可变负荷：** 基本负荷全天候运行——工艺制冷、服务器机房、连续制造、占用区域照明。可变负荷与生产计划、占用率和天气（HVAC）相关。负荷系数为0.85的设施（基本负荷为峰值的85%）受益于全天候区块购买。负荷系数为0.45的设施（在占用和非占用之间大幅波动）受益于与高峰/非高峰模式相匹配的形状化产品。
- **Load factor:** Average demand divided by peak demand. Load factor = (Total kWh) / (Peak kW × Hours in period). A high load factor (>0.75) means relatively flat, predictable consumption — easier to procure and lower demand charges per kWh. A low load factor (<0.50) means spiky consumption with a high peak-to-average ratio — demand charges dominate your bill and peak shaving has the highest ROI.
  **负荷系数：** 平均需求除以峰值需求。负荷系数 =（总kWh）/（峰值kW × 期间小时数）。高负荷系数（>0.75）意味着相对平坦、可预测的消费——更容易采购，每kWh需求费用更低。低负荷系数（<0.50）意味着峰值消费与高峰值比——需求费用主导账单，削峰投资回报率最高。
- **Contribution by system:** In manufacturing, typical load breakdown: HVAC 25–35%, production motors/drives 30–45%, compressed air 10–15%, lighting 5–10%, process heating 5–15%. The system contributing most to peak demand is not always the one consuming the most energy — compressed air systems often have the worst peak-to-average ratio due to unloaded running and cycling compressors.
  **系统贡献：** 在制造业中，典型负荷分解：HVAC 25-35%，生产电机/驱动器 30-45%，压缩空气 10-15%，照明 5-10%，工艺加热 5-15%。对峰值需求贡献最大的系统并不总是消耗最多能源的系统——压缩空气系统由于空载运行和循环压缩机通常具有最差的峰值比。

### Market Structures

### Market Structures
### 市场结构

- **Regulated markets:** A single utility provides generation, transmission, and distribution. Rates are set by the state Public Utility Commission (PUC) through periodic rate cases. You cannot choose your electricity supplier. Optimization is limited to tariff selection (switching between available rate schedules), demand charge management, and on-site generation. Approximately 35% of US commercial electricity load is in fully regulated markets.
  **受监管市场：** 单一公用事业公司提供发电、输电和配电。费率由州公用事业委员会（PUC）通过定期费率案件设定。您不能选择电力供应商。优化仅限于关税选择（在可用费率计划之间切换）、需求费用管理和现场发电。美国约35%的商业电力负荷在完全受监管的市场中。
- **Deregulated markets:** Generation is competitive. You can buy electricity from qualified retail energy providers (REPs), directly from the wholesale market (if you have the infrastructure and credit), or through brokers/aggregators. ISOs/RTOs operate the wholesale market: PJM (Mid-Atlantic and Midwest, largest US market), ERCOT (Texas, uniquely isolated grid), CAISO (California), NYISO (New York), ISO-NE (New England), MISO (Central US), SPP (Plains states). Each ISO has different market rules, capacity structures, and pricing mechanisms.
  **放开市场：** 发电具有竞争性。您可以从合格的零售能源提供商（REP）购买电力，直接从批发市场购买（如果您有基础设施和信用），或通过经纪商/聚合商购买。ISO/RTO运营批发市场：PJM（中大西洋和中西部，美国最大市场）、ERCOT（德克萨斯州，独特孤立电网）、CAISO（加利福尼亚州）、NYISO（纽约）、ISO-NE（新英格兰）、MISO（美国中部）、SPP（平原州）。每个ISO有不同的市场规则、容量结构和定价机制。
- **Locational Marginal Pricing (LMP):** Wholesale electricity prices vary by location (node) within an ISO, reflecting generation costs, transmission losses, and congestion. LMP = Energy Component + Congestion Component + Loss Component. A facility at a congested node pays more than one at an uncongested node. Congestion can add $5–$30/MWh to your delivered cost in constrained zones. When evaluating a VPPA, the basis risk between the generator's node and your load zone is driven by congestion patterns.
  **节点边际定价（LMP）：** 批发电力价格在一个ISO内因位置（节点）而异，反映发电成本、输电损耗和拥堵。LMP = 能源组成部分 + 拥堵组成部分 + 损耗组成部分。位于拥堵节点的设施比位于非拥堵节点的设施支付更多。在受限区域，拥堵可能使您的交付成本增加$5-$30/MWh。在评估VPPA时，发电商节点与负荷区域之间的基差风险由拥堵模式驱动。

### Sustainability Reporting

### Sustainability Reporting
### 可持续发展报告

- **Scope 2 emissions — two methods:** The GHG Protocol requires dual reporting. Location-based: uses average grid emission factor for your region (eGRID in the US). Market-based: reflects your procurement choices — if you buy RECs or have a PPA, your market-based emissions decrease. Most companies targeting RE100 or SBTi approval focus on market-based Scope 2.
  **范围2排放——两种方法：** GHG协议要求双重报告。基于位置：使用您所在地区的平均电网排放因子（美国使用eGRID）。基于市场：反映您的采购选择——如果您购买REC或拥有PPA，您的市场基础排放会减少。大多数以RE100或SBTi审批为目标的公司专注于市场基础范围2。
- **RE100:** A global initiative where companies commit to 100% renewable electricity. Requires annual reporting of progress. Acceptable instruments: physical PPAs, VPPAs with RECs, utility green tariff programs, unbundled RECs (though RE100 is tightening additionality requirements), and on-site generation.
  **RE100：** 一项全球倡议，公司承诺100%使用可再生电力。需要年度进展报告。可接受的工具：实物PPA、带REC的VPPA、公用事业绿色关税项目、独立REC（尽管RE100正在收紧额外性要求）和现场发电。
- **CDP and SBTi:** CDP (formerly Carbon Disclosure Project) scores corporate climate disclosure. Energy procurement data feeds your CDP Climate Change questionnaire directly — Section C8 (Energy). SBTi (Science Based Targets initiative) validates that your emissions reduction targets align with Paris Agreement goals. Procurement decisions that lock in fossil-heavy supply for 10+ years can conflict with SBTi trajectories.
  **CDP和SBTi：** CDP（前身是碳信息披露项目）评估企业气候信息披露。能源采购数据直接填入您的CDP气候变化问卷——C8部分（能源）。SBTi（科学碳目标倡议）验证您的减排目标是否与巴黎协定目标一致。锁定化石燃料供应10年以上的采购决策可能与SBTi路线冲突。

### Risk Management

### Risk Management
### 风险管理

- **Hedging approaches:** Layered procurement is the primary hedge. Supplement with financial hedges (swaps, options, heat rate call options) for specific exposures. Buy put options on wholesale electricity to cap your index pricing exposure — a $50/MWh put costs $2–$5/MWh premium but prevents the catastrophic tail risk of $200+/MWh wholesale spikes.
  **对冲方法：** 分层采购是对冲的主要手段。针对特定敞口辅以金融对冲（互换、期权、热率看涨期权）。购买批发电力的看跌期权以限制您的指数定价敞口——$50/MWh的看跌期权成本$2-$5/MWh溢价，但防止$200+/MWh批发飙升的灾难性尾部风险。
- **Budget certainty vs. market exposure:** The fundamental tradeoff. Fixed-price contracts provide certainty at a premium. Index contracts provide lower average cost at higher variance. Most sophisticated C&I buyers land on 60–80% hedged, 20–40% index — the exact ratio depends on the company's financial profile, treasury risk tolerance, and whether energy is a material input cost (manufacturers) or an overhead line item (offices).
  **预算确定性 vs. 市场敞口：** 根本的权衡。固定价格合同以溢价提供确定性。指数合同以更高方差提供更低的平均成本。大多数成熟的C&I买家锁定60-80%对冲、20-40%指数——确切比例取决于公司的财务状况、财政部风险承受能力以及能源是主要投入成本（制造商）还是一般管理费用项目（办公室）。
- **Weather risk:** Heating degree days (HDD) and cooling degree days (CDD) drive consumption variance. A winter 15% colder than normal can increase natural gas costs 25–40% above budget. Weather derivatives (HDD/CDD swaps and options) can hedge volumetric risk — but most C&I buyers manage weather risk through budget reserves rather than financial instruments.
  **天气风险：** 度日供热量（HDD）和度日制冷量（CDD）驱动消费差异。比正常情况冷15%的冬天可能使天然气成本比预算高出25-40%。天气衍生品（HDD/CDD互换和期权）可以对冲数量风险——但大多数C&I买家通过预算储备而非金融工具来管理天气风险。
- **Regulatory risk:** Tariff changes through rate cases, capacity market reform (PJM's capacity market has restructured pricing 3 times since 2015), carbon pricing legislation, and net metering policy changes can all shift the economics of your procurement strategy mid-contract.
  **监管风险：** 费率案件中的关税变更、容量市场改革（PJM的容量市场自2015年以来已3次重组定价）、碳定价立法和净计量政策变更都可能改变合同中期采购策略的经济性。

## Decision Frameworks

## Decision Frameworks
## 决策框架

### Procurement Strategy Selection

### Procurement Strategy Selection
### 采购策略选择

When choosing between fixed, index, and block-and-index for a contract renewal:
在合同续签时选择固定、指数和区块加指数时：

1. **What is the company's tolerance for budget variance?** If energy cost variance >5% of budget triggers a management review, lean fixed. If the company can absorb 15–20% variance without financial stress, index or block-and-index is viable.
   **公司的预算差异容忍度是多少？** 如果能源成本差异>预算的5%会触发管理层审查，则倾向于固定。如果公司可以在没有财务压力的情况下吸收15-20%的差异，则指数或区块加指数是可行的。
2. **Where is the market in the price cycle?** If forward curves are at the bottom third of the 5-year range, lock in more fixed (buy the dip). If forwards are at the top third, keep more index exposure (don't lock at the peak). If uncertain, layer.
   **市场在价格周期中的位置是什么？** 如果远期曲线处于5年区间的下三分之一，则锁定更多固定（逢低买入）。如果远期处于上三分之一，则保持更多指数敞口（不要在顶部锁定）。如果不确定，则分层。
3. **What is the contract tenor?** For 12-month terms, fixed vs. index matters less — the premium is small and the exposure period is short. For 36+ month terms, the risk premium on fixed pricing compounds and the probability of overpaying increases. Lean hybrid or layered for longer tenors.
   **合同期限是什么？** 对于12个月期限，固定vs.指数的影响较小——溢价小，敞口期短。对于36个月以上的期限，固定定价的风险溢价会累积，过度支付的可能性增加。对于较长期限倾向于混合或分层。
4. **What is the facility's load factor?** High load factor (>0.75): block-and-index works well — buy flat blocks around the clock. Low load factor (<0.50): shaped blocks or TOU-indexed products better match the load profile.
   **设施的负荷系数是多少？** 高负荷系数（>0.75）：区块加指数效果很好——全天候购买扁平区块。低负荷系数（<0.50）：形状化区块或TOU指数产品更好地匹配负荷形态。

### PPA Evaluation

### PPA Evaluation
### PPA评估

Before committing to a 10–25 year PPA, evaluate:
在承诺10-25年PPA之前，评估：

1. **Does the project economics pencil?** Compare the PPA strike price to the forward curve for the contract tenor. A $35/MWh solar PPA against a $45/MWh forward curve has $10/MWh positive spread. But model the full term — a 20-year PPA at $35/MWh that was in-the-money at signing can go underwater if wholesale prices drop below the strike due to overbuilding of renewables in the region.
   **项目经济性是否可行？** 将PPA执行价格与合同期限的远期曲线进行比较。$35/MWh太阳能PPA与$45/MWh远期曲线相比有$10/MWh的正价差。但要建模整个期限——如果在签署时赚钱的$35/MWh的20年期PPA可能因该地区可再生能源过度建设而使批发价格跌破执行价格而变得不划算。
2. **What is the basis risk?** If the generator is in West Texas (ERCOT West) and your load is in Houston (ERCOT Houston), congestion between the two zones can create a persistent basis spread of $3–$12/MWh that erodes the PPA value. Require the developer to provide 5+ years of historical basis data between the project node and your load zone.
   **基差风险是什么？** 如果发电商在德克萨斯州西部（ERCOT West）而您的负荷在休斯顿（ERCOT Houston），两个区域之间的拥堵可能产生持续的$3-$12/MWh的基差，侵蚀PPA价值。要求开发商提供项目节点与负荷区域之间5年以上的历史基差数据。
3. **What is the curtailment exposure?** ERCOT curtails wind at 3–8% annually; CAISO curtails solar at 5–12% in spring months. If the PPA settles on generated (not scheduled) volumes, curtailment reduces your REC delivery and changes the economics. Negotiate a curtailment cap or a settlement structure that doesn't penalize you for grid-operator curtailment.
   **削减敞口是什么？** ERCOT每年削减风能3-8%；CAISO在春季月份削减太阳能5-12%。如果PPA按发电量（非计划量）结算，削减会减少您的REC交付并改变经济性。协商削减上限或结算结构，不要因电网运营商削减而惩罚您。
4. **What are the credit requirements?** Developers typically require investment-grade credit or a letter of credit / parent guarantee for long-term PPAs. A $50M notional VPPA may require a $5–$10M LC, tying up capital. Factor the LC cost into your PPA economics.
   **信用要求是什么？** 开发商通常要求投资级信用或信用证/母公司担保用于长期PPA。$5000万名义VPPA可能需要$500-$1000万信用证，占用资本。将信用证成本纳入PPA经济性。

### Demand Charge Mitigation ROI

### Demand Charge Mitigation ROI
### 需求费用缓解投资回报率

Evaluate demand charge reduction investments using total stacked value:
使用总叠加值评估需求费用削减投资：

1. Calculate current demand charges: Peak kW × demand rate × 12 months.
   计算当前需求费用：峰值kW × 需求费率 × 12个月。
2. Estimate achievable peak reduction from the proposed intervention (battery, load control, DR).
   估计拟议干预措施（电池、负荷控制、DR）可实现的峰值削减。
3. Value the reduction across all applicable tariff components: demand charges + capacity tag reduction (takes effect following delivery year) + TOU energy arbitrage + DR program revenue.
   评估所有适用关税组成部分的削减价值：需求费用 + 容量标签减少（在下个交付年生效）+ TOU能源套利 + DR项目收入。
4. If simple payback < 5 years with stacked value, the investment is typically justified. If 5–8 years, it's marginal and depends on capital availability. If > 8 years on stacked value, the economics don't work unless driven by sustainability mandate.
   如果使用叠加值的简单回收期<5年，投资通常是合理的。如果5-8年，则处于边缘，取决于资本可用性。如果叠加值>8年，除非由可持续发展授权驱动，否则经济性不成立。

### Market Timing

### Market Timing
### 市场时机

Never try to "call the bottom" on energy markets. Instead:
永远不要试图"判断市场底部"。相反：

- Monitor the forward curve relative to the 5-year historical range. When forwards are in the bottom quartile, accelerate procurement (buy tranches faster than your layering schedule). When in the top quartile, decelerate (let existing tranches roll and increase index exposure).
  监控相对于5年历史区间的远期曲线。当远期处于下四分位时，加速采购（比您的分层计划更快地购买批次）。当处于上四分位时，放慢（让现有批次滚动并增加指数敞口）。
- Watch for structural signals: new generation additions (bearish for prices), plant retirements (bullish), pipeline constraints for natural gas (regional price divergence), and capacity market auction results (drives future capacity charges).
  关注结构性信号：新发电增加（对价格看跌）、电厂退役（看涨）、天然气管道约束（区域价格分歧）和容量市场拍卖结果（驱动未来容量费用）。

Use the procurement sequence above as the decision framework baseline and adapt it to your tariff structure, procurement calendar, and board-approved hedge limits.
将上述采购顺序作为决策框架基础，并根据您的关税结构、采购日历和董事会批准的对冲限制进行调整。

## Key Edge Cases

## Key Edge Cases
## 关键边缘案例

These are situations where standard procurement playbooks produce poor outcomes. Brief summaries are included here so you can expand them into project-specific playbooks if needed.
这些是标准采购 playbook 产生不良结果的情况。在此提供简要总结，以便您可以将它们扩展为项目特定的 playbook。

1. **ERCOT price spike during extreme weather:** Winter Storm Uri demonstrated that index-priced customers in ERCOT face catastrophic tail risk. A 5 MW facility on index pricing incurred $1.5M+ in a single week. The lesson is not "avoid index pricing" — it's "never go unhedged into winter in ERCOT without a price cap or financial hedge."
   **极端天气期间的ERCOT价格飙升：** 冬季风暴Uri表明，ERCOT中按指数定价的客户面临灾难性的尾部风险。一个5 MW的指数定价设施在一周内承担了超过150万美元的费用。教训不是"避免指数定价"——而是"在ERCOT中进入冬季时不要在没有价格上限或金融对冲的情况下不加保护。"
2. **Virtual PPA basis risk in a congested zone:** A VPPA with a wind farm in West Texas settling against Houston load zone prices can produce persistent negative settlements of $3–$12/MWh due to transmission congestion, turning an apparently favorable PPA into a net cost.
   **拥堵区域的虚拟PPA基差风险：** 在西德克萨斯州与休斯顿负荷区价格结算的风电场VPPA可能因输电拥堵产生持续的$3-$12/MWh负结算，使表面上有利的PPA变成净成本。
3. **Demand charge ratchet trap:** A facility modification (new production line, chiller replacement startup) creates a single month's peak 50% above normal. The tariff's 80% ratchet clause locks elevated billing demand for 11 months. A $200K annual cost increase from a single 15-minute interval.
   **需求费用棘轮陷阱：** 设施改造（新生产线、更换冷却器启动）使单月峰值比正常情况高50%。关税的80%棘轮条款将抬高的计费需求锁定11个月。一次15分钟间隔的年度成本增加$20万。
4. **Utility rate case filing mid-contract:** Your fixed-price supply contract covers the energy component, but T&D and rider charges flow through. A utility rate case adds $0.012/kWh to delivery charges — a $150K annual increase on a 12 MW facility that your "fixed" contract doesn't protect against.
   **合同中期的公用事业费率案件申报：** 您的固定价格供应合同涵盖能源组成部分，但T&D和附加费用会转嫁。公用事业费率案件在配送费用上增加$0.012/kWh——一个12 MW设施的年度增加$15万，"固定"合同无法保护。
5. **Negative LMP pricing affecting PPA economics:** During high-wind or high-solar periods, wholesale prices go negative at the generator's node. Under some PPA structures, you owe the developer the settlement difference on negative-price intervals, creating surprise payments.
   **负LMP定价影响PPA经济性：** 在高风或高太阳能期间，批发价格在发电商节点变为负值。在某些PPA结构下，您欠开发商负价格间隔的结算差额，产生意外付款。
6. **Behind-the-meter solar cannibalizing demand response value:** On-site solar reduces your average consumption but may not reduce your peak (peaks often occur on cloudy late afternoons). If your DR baseline is calculated on recent consumption, solar reduces the baseline, which reduces your DR curtailment capacity and associated revenue.
   **电表后太阳能侵蚀需求响应价值：** 现场太阳能减少您的平均消费，但可能不会减少您的峰值（峰值通常发生在多云的下午晚些时候）。如果您的DR基线基于近期消费计算，太阳能会减少基线，从而减少您的DR削减能力和相关收入。
7. **Capacity market obligation surprise:** In PJM, your capacity tag (PLC) is set by your load during the prior year's 5 coincident peak hours. If you ran backup generators or increased production during a heat wave that happened to include peak hours, your PLC spikes, and capacity charges increase 20–40% the following delivery year.
   **容量市场义务意外：** 在PJM中，您的容量标签（PLC）由您在上一年度5个联合峰值时段的负荷设定。如果您在恰好包含峰值时段的热浪期间运行备用发电机或增加产量，您的PLC会飙升，容量费用在下个交付年增加20-40%。
8. **Deregulated market re-regulation risk:** A state legislature proposes re-regulation after a price spike event. If enacted, your competitively procured supply contract may be voided, and you revert to utility tariff rates — potentially at higher cost than your negotiated contract.
   **放开市场再监管风险：** 州立法机构在价格飙升事件后提议再监管。如果颁布，您竞争性采购的供应合同可能被撤销，您将恢复为公用事业关税费率——可能比您协商的合同成本更高。

## Communication Patterns

## Communication Patterns
## 沟通模式

### Supplier Negotiations

### Supplier Negotiations
### 供应商谈判

Energy supplier negotiations are multi-year relationships. Calibrate tone:
能源供应商谈判是多年关系。调整语气：

- **RFP issuance:** Professional, data-rich, competitive. Provide complete interval data and load profiles. Suppliers who can't model your load accurately will pad their margins. Transparency reduces risk premiums.
  **RFP发布：** 专业、数据丰富、有竞争力。提供完整的间隔数据和负荷曲线。无法准确建模负荷的供应商会扩大其利润空间。透明度降低风险溢价。
- **Contract renewal:** Lead with relationship value and volume growth, not price demands. "We've valued the partnership over the past 36 months and want to discuss renewal terms that reflect both market conditions and our growing portfolio."
  **合同续签：** 以关系价值和数量增长为先，而非价格要求。"我们在过去36个月重视合作伙伴关系，希望讨论反映市场状况和我们不断增长的组合的续签条款。"
- **Price challenges:** Reference specific market data. "ICE forward curves for 2027 are showing $42/MWh for AEP Dayton Hub. Your quote of $48/MWh reflects a 14% premium to the curve — can you help us understand what's driving that spread?"
  **价格挑战：** 引用具体市场数据。"ICE 2027年远期曲线显示AEP Dayton Hub为$42/MWh。您的报价$48/MWh相比曲线溢价14%——您能帮助我们理解是什么推动了这一价差吗？"

### Internal Stakeholders

### Internal Stakeholders
### 内部利益相关者

- **Finance/treasury:** Quantify decisions in terms of budget impact, variance, and risk. "This block-and-index structure provides 75% budget certainty with a modeled worst-case variance of ±$400K against a $12M annual energy budget."
  **财务/财政部：** 从预算影响、差异和风险角度量化决策。"这种区块加指数结构提供75%的预算确定性，在1200万美元年度能源预算下建模的最坏情况差异为±$40万。"
- **Sustainability:** Map procurement decisions to Scope 2 targets. "This PPA delivers 50,000 MWh of bundled RECs annually, representing 35% of our RE100 target."
  **可持续发展：** 将采购决策映射到范围2目标。"该PPA每年提供50,000 MWh捆绑REC，占我们RE100目标的35%。"
- **Operations:** Focus on operational requirements and constraints. "We need to reduce peak demand by 400 kW during summer afternoons — here are three options that don't affect production schedules."
  **运营：** 关注运营要求和约束。"我们需要在夏季下午减少400 kW的峰值需求——以下是三个不影响生产计划的选项。"

Use the communication examples here as starting points and adapt them to your supplier, utility, and executive stakeholder workflows.
将此处沟通示例作为起点，并根据您的供应商、公用事业和行政利益相关者工作流程进行调整。

## Escalation Protocols

## Escalation Protocols
## 升级协议

| Trigger | Action | Timeline |
|---|---|---|
| Trigger | Action | Timeline |
| 触发条件 | 行动 | 时间线 |
| Wholesale prices exceed 2× budget assumption for 5+ consecutive days | Notify finance, evaluate hedge position, consider emergency fixed-price procurement | Within 24 hours |
| 批发价格连续5天以上超过预算假设的2倍 | 通知财务，评估对冲头寸，考虑紧急固定价格采购 | 24小时内 |
| Supplier credit downgrade below investment grade | Review contract termination provisions, assess replacement supplier options | Within 48 hours |
| 供应商信用评级降至投资级以下 | 审查合同终止条款，评估替代供应商选项 | 48小时内 |
| Utility rate case filed with >10% proposed increase | Engage regulatory counsel, evaluate intervention filing | Within 1 week |
| 公用事业费率案件申报拟议增加>10% | 聘请监管律师，评估干预申报 | 1周内 |
| Demand peak exceeds ratchet threshold by >15% | Investigate root cause with operations, model billing impact, evaluate mitigation | Within 24 hours |
| 需求峰值超过棘轮阈值>15% | 与运营部门调查根本原因，建模计费影响，评估缓解措施 | 24小时内 |
| PPA developer misses REC delivery by >10% of contracted volume | Issue notice of default per contract, evaluate replacement REC procurement | Within 5 business days |
| PPA开发商REC交付缺失超过合同量的>10% | 按合同发出违约通知，评估替代REC采购 | 5个工作日内 |
| Capacity tag (PLC) increases >20% from prior year | Analyze coincident peak intervals, model capacity charge impact, develop peak response plan | Within 2 weeks |
| 容量标签（PLC）比上一年增加>20% | 分析联合峰值间隔，建模容量费用影响，制定峰值响应计划 | 2周内 |
| Regulatory action threatens contract enforceability | Engage legal counsel, evaluate contract force majeure provisions | Within 48 hours |
| 监管行动威胁合同可执行性 | 聘请法律顾问，评估合同不可抗力条款 | 48小时内 |
| Grid emergency / rolling blackouts affecting facilities | Activate emergency load curtailment, coordinate with operations, document for insurance | Immediate |
| 影响设施的电网紧急情况/轮流停电 | 激活紧急负荷削减，与运营协调，为保险记录 | 立即 |

### Escalation Chain

### Escalation Chain
### 升级链

Energy Analyst → Energy Procurement Manager (24 hours) → Director of Procurement (48 hours) → VP Finance/CFO (>$500K exposure or long-term commitment >5 years)
能源分析师 → 能源采购经理（24小时）→ 采购总监（48小时）→ 财务副总裁/CFO（>$50万敞口或长期承诺>5年）

## Performance Indicators

## Performance Indicators
## 绩效指标

Track monthly, review quarterly with finance and sustainability:
每月跟踪，每季度与财务和可持续发展审查：

| Metric | Target | Red Flag |
|---|---|---|
| Metric | Target | Red Flag |
| 指标 | 目标 | 红旗 |
| Weighted average energy cost vs. budget | Within ±5% | >10% variance |
| 加权平均能源成本与预算 | ±5%以内 | >10%差异 |
| Procurement cost vs. market benchmark (forward curve at time of execution) | Within 3% of market | >8% premium |
| 采购成本与市场基准（执行时远期曲线） | 在市场3%以内 | >8%溢价 |
| Demand charges as % of total bill | <25% (manufacturing) | >35% |
| 需求费用占总账单百分比 | <25%（制造业） | >35% |
| Peak demand vs. prior year (weather-normalized) | Flat or declining | >10% increase |
| 峰值需求与上年（天气正常化） | 持平或下降 | >10%增加 |
| Renewable energy % (market-based Scope 2) | On track to RE100 target year | >15% behind trajectory |
| 可再生能源百分比（市场基础范围2） | 按RE100目标年进度 | 落后轨迹>15% |
| Supplier contract renewal lead time | Signed ≥90 days before expiry | <30 days before expiry |
| 供应商合同续签提前期 | 到期前≥90天签署 | 到期前<30天 |
| Capacity tag (PLC/ICAP) trend | Flat or declining | >15% YoY increase |
| 容量标签（PLC/ICAP）趋势 | 持平或下降 | 同比增加>15% |
| Budget forecast accuracy (Q1 forecast vs. actuals) | Within ±7% | >12% miss |
| 预算预测准确性（Q1预测 vs. 实际） | ±7%以内 | >12%偏差 |

## Additional Resources

## Additional Resources
## 其他资源

- Maintain an internal hedge policy, approved counterparty list, and tariff-change calendar alongside this skill.
  与此技能一起维护内部对冲政策、批准对手方列表和关税变更日历。
- Keep facility-specific load shapes and utility contract metadata close to the planning workflow so recommendations stay grounded in real demand patterns.
  将设施特定的负荷曲线和公用事业合同元数据保持在规划工作流程附近，以便建议基于真实需求模式。
