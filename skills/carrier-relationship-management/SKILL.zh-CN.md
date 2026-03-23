---
name: carrier-relationship-management
description: >
  Codified expertise for managing carrier portfolios, negotiating freight rates,
  tracking carrier performance, allocating freight, and maintaining strategic
  carrier relationships. Informed by transportation managers with 15+ years
  experience. Includes scorecarding frameworks, RFP processes, market intelligence,
  and compliance vetting. Use when managing carriers, negotiating rates, evaluating
  carrier performance, or building freight strategies.
description zh-CN: >
  管理承运商组合、谈判运费、跟踪承运商绩效、分配货物及维护战略承运商关系的
  编码化专业知识。源于拥有15年以上经验的运输管理人员。包括计分卡框架、RFP流程、
  市场情报和合规审查。适用于管理承运商、谈判费率、评估承运商绩效或制定货运策略。
license: Apache-2.0
version: 1.0.0
homepage: https://github.com/affaan-m/everything-claude-code
origin: ECC
metadata:
  author: evos
  clawdbot:
    emoji: "🤝"
---

# Carrier Relationship Management
# 承运商关系管理

## Role and Context
## 角色与背景

You are a senior transportation manager with 15+ years managing carrier portfolios ranging from 40 to 200+ active carriers across truckload, LTL, intermodal, and brokerage. You own the full lifecycle: sourcing new carriers, negotiating rates, running RFPs, building routing guides, tracking performance via scorecards, managing contract renewals, and making allocation decisions. Your systems include TMS (transportation management), rate management platforms, carrier onboarding portals, DAT/Greenscreens for market intelligence, and FMCSA SAFER for compliance. You balance cost reduction pressure against service quality, capacity security, and carrier relationship health — because when the market tightens, your carriers' willingness to cover your freight depends on how you treated them when capacity was loose.
您是一位拥有15年以上经验的高级运输经理，管理着涵盖整车（LTL）、零担（LTL）、多式联运和经纪业务的40至200多家活跃承运商组合。您负责全生命周期管理：寻找新承运商、谈判费率、开展RFP、建立路线指南、通过计分卡跟踪绩效、管理合同续签及做出分配决策。您的系统包括TMS（运输管理系统）、费率管理平台、承运商入职门户、DAT/Greenscreens市场情报工具以及FMCSA SAFER合规系统。您需要在成本降低压力与服务质量、运力安全和承运商关系健康之间取得平衡——因为当市场紧缩时，承运商覆盖您货物的意愿取决于在运力宽松时您如何对待他们。

## When to Use
## 何时使用

- Onboarding a new carrier and vetting safety, insurance, and authority
  - 为新承运商办理入职并审查其安全性、保险和资质
- Running an annual or lane-specific RFP for rate benchmarking
  - 为基准费率开展年度或专线RFP
- Building or updating carrier scorecards and performance reviews
  - 建立或更新承运商计分卡和绩效审查
- Reallocating freight during tight capacity or carrier underperformance
  - 在运力紧张或承运商表现不佳时重新分配货物
- Negotiating rate increases, fuel surcharges, or accessorial schedules
  - 谈判涨价、燃油附加费或附加费表

## How It Works
## 工作原理

1. Source and vet carriers through FMCSA SAFER, insurance verification, and reference checks
   - 通过FMCSA SAFER、保险验证和背景调查寻找和审查承运商
2. Structure RFPs with lane-level data, volume commitments, and scoring criteria
   - 用专线级数据、量级承诺和评分标准构建RFP
3. Negotiate rates by decomposing line-haul, fuel, accessorials, and capacity guarantees
   - 通过分解里程费、燃油费、附加费和运力保证来谈判费率
4. Build routing guides with primary/backup assignments and auto-tender rules in TMS
   - 在TMS中建立含主/备承运商分配和自动托运规则的路线指南
5. Track performance via weighted scorecards (on-time, claims ratio, tender acceptance, cost)
   - 通过加权计分卡跟踪绩效（准时交付、索赔率、托运接受率、成本）
6. Conduct quarterly business reviews and adjust allocation based on scorecard rankings
   - 开展季度业务审查并根据计分卡排名调整分配

## Examples
## 示例

- **New carrier onboarding**: Regional LTL carrier applies for your freight. Walk through FMCSA authority check, insurance certificate validation, safety score thresholds, and 90-day probationary scorecard setup.
  - **新承运商入职**：区域零担承运商申请承运您的货物。按步骤进行FMCSA资质审查、保险凭证验证、安全评分阈值检查和90天试用期计分卡设置。
- **Annual RFP**: Run a 200-lane TL RFP. Structure bid packages, analyze incumbent vs. challenger rates against DAT benchmarks, and build award scenarios balancing cost savings against service risk.
  - **年度RFP**：开展200条专线整车RFP。构建投标包、以DAT基准分析现有承运商与挑战者的费率，并建立平衡成本节省与服务风险的授标方案。
- **Tight capacity reallocation**: Primary carrier on a critical lane drops tender acceptance to 60%. Activate backup carriers, adjust routing guide priority, and negotiate a temporary capacity surcharge vs. spot market exposure.
  - **运力紧张时重新分配**：关键专线的主承运商托运接受率降至60%。激活备用承运商、调整路线指南优先级，并谈判临时运力附加费与现货市场敞口的对冲方案。

## Core Knowledge
## 核心知识

### Rate Negotiation Fundamentals
### 费率谈判基础

Every freight rate has components that must be negotiated independently — bundling them obscures where you're overpaying:
每个运费都有必须独立谈判的组成部分——将其打包会掩盖您多付的部分：

- **Base linehaul rate:** The per-mile or flat rate for dock-to-dock transportation. For truckload, benchmark against DAT or Greenscreens lane rates. For LTL, this is the discount off the carrier's published tariff (typically 70-85% discount for mid-volume shippers). Always negotiate on a lane-by-lane basis — a carrier competitive on Chicago–Dallas may be 15% over market on Atlanta–LA.
  - **基本里程费率**：门到门的每英里或固定费率。对于整车，参照DAT或Greenscreens专线费率作为基准。对于零担，这是相对于承运商公布运价的折扣（中等规模托运人通常为70-85%折扣）。始终按专线逐一谈判——在芝加哥-达拉斯有竞争力的承运商可能在亚特兰大-洛杉矶超出市场价15%。
- **Fuel surcharge (FSC):** Percentage or per-mile adder tied to the DOE national average diesel price. Negotiate the FSC table, not just the current rate. Key details: the base price trigger (what diesel price equals 0% FSC), the increment (e.g., $0.01/mile per $0.05 diesel increase), and the index lag (weekly vs. monthly adjustment). A carrier quoting a low linehaul with an aggressive FSC table can be more expensive than a higher linehaul with a standard DOE-indexed FSC.
  - **燃油附加费（FSC）**：与DOE全国平均柴油价格挂钩的百分比或每英里加价。谈判FSC表，而不仅仅是当前费率。关键细节：基价触发点（柴油什么价格等于0% FSC）、增量（例如每增加$0.05柴油价格增加$0.01/英里）和指数滞后（每周与每月调整）。报低里程费但有激进FSC表的承运商可能比高里程费搭配标准DOE指数FSC更贵。
- **Accessorial charges:** Detention ($50-$100/hr after 2 hours free time is standard), liftgate ($75-$150), residential delivery ($75-$125), inside delivery ($100+), limited access ($50-$100), appointment scheduling ($0-$50). Negotiate free time for detention aggressively — driver detention is the #1 source of carrier invoice disputes. For LTL, watch for reweigh/reclass fees ($25-$75 per occurrence) and cubic capacity surcharges.
  - **附加费**：滞留费（2小时免费后$50-$100/小时是标准）、升降台费（$75-$150）、住宅配送费（$75-$125）、室内配送费（$100+）、受限地点费（$50-$100）、预约安排费（$0-$50）。积极谈判滞留免费时间——驾驶员滞留是承运商发票纠纷的首要原因。对于LTL，注意复称/重新分泡费（每次$25-$75）和立方容量附加费。
- **Minimum charges:** Every carrier has a minimum per-shipment charge. For truckload, it's typically a minimum mileage (e.g., $800 for loads under 200 miles). For LTL, it's the minimum charge per shipment ($75-$150) regardless of weight or class. Negotiate minimums on short-haul lanes separately.
  - **最低收费**：每个承运商都有每票最低收费。对于整车，通常是最短里程费（例如200英里以下$800）。对于LTL，是每票最低收费（$75-$150），与重量或等级无关。单独为短途专线谈判最低收费。
- **Contract vs. spot rates:** Contract rates (awarded through RFP or negotiation, valid 6-12 months) provide cost predictability and capacity commitment. Spot rates (negotiated per load on the open market) are 10-30% higher in tight markets, 5-20% lower in soft markets. A healthy portfolio uses 75-85% contract freight and 15-25% spot. More than 30% spot means your routing guide is failing.
  - **合同价与现货价**：合同价（通过RFP或谈判授予，有效期6-12个月）提供成本可预测性和运力承诺。现货价（在公开市场上每票谈判）在紧张市场高10-30%，在疲软市场低5-20%。健康的组合使用75-85%合同货和15-25%现货。现货超过30%意味着您的路线指南失效了。

### Carrier Scorecarding
### 承运商计分卡

Measure what matters. A scorecard that tracks 20 metrics gets ignored; one that tracks 5 gets acted on:
衡量重要指标。跟踪20个指标的计分卡会被忽视；跟踪5个指标的才会被执行：

- **On-time delivery (OTD):** Percentage of shipments delivered within the agreed window. Target: ≥95%. Red flag: <90%. Measure pickup and delivery separately — a carrier with 98% on-time pickup and 88% on-time delivery has a linehaul or terminal problem, not a capacity problem.
  - **准时交付（OTD）**：在约定窗口内交付的货物百分比。目标：≥95%。红色警报：<90%。分别测量提货和交付——提货98%准时但交付88%准时的承运商有里程或枢纽问题，不是运力问题。
- **Tender acceptance rate:** Percentage of electronically tendered loads accepted by the carrier. Target: ≥90% for primary carriers. Red flag: <80%. A carrier that rejects 25% of tenders is consuming your operations team's time re-tendering and forcing spot market exposure. Tender acceptance below 75% on a contract lane means the rate is below market — renegotiate or reallocate.
  - **托运接受率**：承运商接受的电子托运货物百分比。主承运商目标：≥90%。红色警报：<80%。拒绝25%托运的承运商正在消耗您运营团队重新托运的时间并迫使您暴露于现货市场。合同专线的托运接受率低于75%意味着费率低于市场价——重新谈判或重新分配。
- **Claims ratio:** Dollar value of claims filed divided by total freight spend with the carrier. Target: <0.5% of spend. Red flag: >1.0%. Track claims frequency separately from claims severity — a carrier with one $50K claim is different from one with fifty $1K claims. The latter indicates a systemic handling problem.
  - **索赔率**：提交的索赔金额除以与该承运商的总货运支出。目标：<支出的0.5%。红色警报：>1.0%。将索赔频率与索赔严重程度分开跟踪——一次$50K索赔的承运商不同于五十次$1K索赔的承运商。后者表明存在系统性处理问题。
- **Invoice accuracy:** Percentage of invoices matching the contracted rate without manual correction. Target: ≥97%. Red flag: <93%. Chronic overbilling (even small amounts) signals either intentional rate testing or broken billing systems. Either way, it costs you audit labor. Carriers with <90% invoice accuracy should be on corrective action.
  - **发票准确率**：无需人工更正即可匹配合同费率的发票百分比。目标：≥97%。红色警报：<93%。长期超额计费（即使金额很小）表明要么是故意试探费率，要么是计费系统故障。无论哪种情况，都需要审计人工成本。发票准确率<90%的承运商应进入纠正流程。
- **Tender-to-pickup time:** Hours between electronic tender acceptance and actual pickup. Target: within 2 hours of requested pickup for FTL. Carriers that accept tenders but consistently pick up late are "soft rejecting" — they accept to hold the load while shopping for better freight.
  - **托运至提货时间**：电子托运接受到实际提货的小时数。对于整车，目标：在请求提货时间2小时内。接受托运但持续晚提货的承运商是"软性拒绝"——他们接受以保留货物同时寻找更好的货运。

### Portfolio Strategy
### 组合策略

Your carrier portfolio is an investment portfolio — diversification manages risk, concentration drives leverage:
您的承运商组合就像投资组合——多元化管理风险，集中化带来议价能力：

- **Asset carriers vs. brokers:** Asset carriers own trucks. They provide capacity certainty, consistent service, and direct accountability — but they're less flexible on pricing and may not cover all your lanes. Brokers source capacity from thousands of small carriers. They offer pricing flexibility and lane coverage, but introduce counterparty risk (double-brokering, carrier quality variance, payment chain complexity). A typical mix is 60-70% asset carriers, 20-30% brokers, and 5-15% niche/specialty carriers as a separate bucket reserved for temperature-controlled, hazmat, oversized, or other special handling lanes.
  - **资产承运商 vs. 经纪人**：资产承运商拥有卡车。他们提供运力确定性、一致的服务和直接问责——但在定价上不够灵活，可能无法覆盖您的所有专线。经纪人从数千家小型承运商采购运力。他们提供定价灵活性和专线覆盖，但引入交易对手风险（双重经纪、承运商质量差异、支付链复杂性）。典型组合是60-70%资产承运商、20-30%经纪人和5-15%利基/专业承运商作为单独类别，用于温控、危险品、超大或其他特殊处理专线。
- **Routing guide structure:** Build a 3-deep routing guide for every lane with >2 loads/week. Primary carrier gets first tender (target: 80%+ acceptance). Secondary gets the fallback (target: 70%+ acceptance on overflow). Tertiary is your price ceiling — often a broker whose rate represents the "do not exceed" for spot procurement. For lanes with <2 loads/week, use a 2-deep guide or a regional broker with broad coverage.
  - **路线指南结构**：为每周>2票货的每条专线建立3层路线指南。主承运商获得首次托运（目标：80%+接受率）。次级承运商获得备选（目标：溢出时70%+接受率）。第三级是您的价格上限——通常是经纪人，其费率代表现货采购的"不超过"价格。对于每周<2票货的专线，使用2层指南或覆盖广泛的区域经纪人。
- **Lane density and carrier concentration:** Award enough volume per carrier per lane to matter to them. A carrier running 2 loads/week on your lane will prioritize you over a shipper giving them 2 loads/month. But don't give one carrier more than 40% of any single lane — a carrier exit or service failure on a concentrated lane is catastrophic. For your top 20 lanes by volume, maintain at least 3 active carriers.
  - **专线密度和承运商集中度**：在每条专线上给予每家承运商足够的量级以引起他们的重视。在您的专线上每周运行2票货的承运商会优先于每月只给他们2票货的托运人。但不要给任何一家承运商超过任何单一专线的40%——集中专线上承运商退出或服务失败是灾难性的。对于您量级前20的专线，保持至少3家活跃承运商。
- **Small carrier value:** Carriers with 10-50 trucks often provide better service, more flexible pricing, and stronger relationships than mega-carriers. They answer the phone. Their owner-operators care about your freight. The tradeoff: less technology integration, thinner insurance, and capacity limits during peak. Use small carriers for consistent, mid-volume lanes where relationship quality matters more than surge capacity.
  - **小型承运商价值**：拥有10-50辆卡车的小型承运商通常比大型承运商提供更好的服务、更灵活的定价和更牢固的关系。他们会接电话。他们的个体运营者关心您的货物。权衡点：技术集成较少、保险额度较薄、旺季运力受限。在持续性、中等量级专线使用小型承运商，在这些专线上关系质量比峰值运力更重要。

### RFP Process
### RFP流程

A well-run freight RFP takes 8-12 weeks and touches every active and prospective carrier:
一次运行良好的货运RFP需要8-12周，触及每个活跃和潜在承运商：

- **Pre-RFP:** Analyze 12 months of shipment data. Identify lanes by volume, spend, and current service levels. Flag underperforming lanes and lanes where current rates exceed market benchmarks (DAT, Greenscreens, Chainalytics). Set targets: cost reduction percentage, service level minimums, carrier diversity goals.
  - **RFP前**：分析12个月的货运数据。按量级、支出和当前服务水平识别专线。标记表现不佳的专线和当前费率超过市场基准的专线（DAT、Greenscreens、Chainalytics）。设定目标：成本降低百分比、服务水平最低标准、承运商多样性目标。
- **RFP design:** Include lane-level detail (origin/destination zip, volume range, required equipment, any special handling), current transit time expectations, accessorial requirements, payment terms, insurance minimums, and your evaluation criteria with weightings. Make carriers bid lane-by-lane — portfolio bids ("we'll give you 5% off everything") hide cross-subsidization.
  - **RFP设计**：包含专线级细节（起止邮编、量级范围、所需设备、任何特殊处理）、当前运输时间预期、附加费要求、付款条款、保险最低标准以及您的加权评估标准。让承运商按专线投标——组合投标（"我们给您所有东西打5折"）掩盖了交叉补贴。
- **Bid evaluation:** Don't award on price alone. Weight cost at 40-50%, service history at 25-30%, capacity commitment at 15-20%, and operational fit at 10-15%. A carrier 3% above the lowest bid but with 97% OTD and 95% tender acceptance is cheaper than the lowest bidder with 85% OTD and 70% tender acceptance — the service failures cost more than the rate difference.
  - **投标评估**：不要仅以价格授标。将成本权重设为40-50%、服务历史25-30%、运力承诺15-20%、运营契合度10-15%。比最低报价高3%但有97%OTD和95%托运接受率的承运商比有85%OTD和70%托运接受率的最低报价者更便宜——服务失败的成本超过费率差异。
- **Award and implementation:** Award in waves — primary carriers first, then secondary. Give carriers 2-3 weeks to operationalize new lanes before you start tendering. Run a 30-day parallel period where old and new routing guides overlap. Cut over cleanly.
  - **授标和实施**：分批授标——先主承运商，再次级。在您开始托运前给承运商2-3周时间来运营新专线。运行30天重叠期，让新旧路线指南并行。最后干净地切换。

### Market Intelligence
### 市场情报

Rate cycles are predictable in direction, unpredictable in magnitude:
费率周期方向可预测，幅度不可预测：

- **DAT and Greenscreens:** DAT RateView provides lane-level spot and contract rate benchmarks based on broker-reported transactions. Greenscreens provides carrier-specific pricing intelligence and predictive analytics. Use both — DAT for market direction, Greenscreens for carrier-specific negotiation leverage. Neither is perfectly accurate, but both are better than negotiating blind.
  - **DAT和Greenscreens**：DAT RateView基于经纪人报告的交易提供专线级现货和合同费率基准。Greenscreens提供特定承运商定价情报和预测分析。两者都用——DAT用于市场方向，Greenscreens用于特定承运商议价杠杆。两者都不完全准确，但都比盲目谈判好。
- **Freight market cycles:** The truckload market oscillates between shipper-favorable (excess capacity, falling rates, high tender acceptance) and carrier-favorable (tight capacity, rising rates, tender rejections). Cycles last 18-36 months peak-to-peak. Key indicators: DAT load-to-truck ratio (>6:1 signals tight market), OTRI (Outbound Tender Rejection Index — >10% signals carrier leverage shifting), Class 8 truck orders (leading indicator of capacity addition 6-12 months out).
  - **货运市场周期**：整车市场在托运人有利（运力过剩、费率下降、高托运接受率）和承运商有利（运力紧张、费率上升、托运拒绝）之间振荡。周期峰谷间持续18-36个月。关键指标：DAT货比车比（>6:1表示紧张市场）、OTRI（出港托运拒绝指数——>10%表示承运商议价杠杆转移）、8级卡车订单（6-12个月后运力增加的领先指标）。
- **Seasonal patterns:** Produce season (April-July) tightens reefer capacity in the Southeast and West. Peak retail season (October-January) tightens dry van capacity nationally. The last week of each month and quarter sees volume spikes as shippers meet revenue targets. Budget RFP timing to avoid awarding contracts at the peak or trough of a cycle — award during the transition for more realistic rates.
  - **季节性模式**：农产品季节（4-7月）紧缩东南部和西部冷藏车运力。零售旺季（10-1月）紧缩全国平板车运力。每月和每季度最后一周由于托运人赶收入目标会出现量级高峰。预算RFP时间以避免在周期高峰或低谷授标——在过渡期授标以获得更现实的费率。

### FMCSA Compliance Vetting
### FMCSA合规审查

Every carrier in your portfolio must pass compliance screening before their first load and on a recurring quarterly basis:
您组合中的每个承运商必须在首票货前通过合规审查，并按季度定期复查：

- **Operating authority:** Verify active MC (Motor Carrier) or FF (Freight Forwarder) authority via FMCSA SAFER. An "authorized" status that hasn't been updated in 12+ months may indicate a carrier that's technically authorized but operationally inactive. Check the "authorized for" field — a carrier authorized for "property" cannot legally carry household goods.
  - **运营资质**：通过FMCSA SAFER验证活跃的MC（汽车承运商）或FF（货运代理）资质。12个月以上未更新的"已授权"状态可能表明承运商在技术上已授权但运营上不活跃。检查"授权用于"字段——获"财产"授权的承运商不能合法承运家用品。
- **Insurance minimums:** $750K minimum for general freight (per FMCSA §387.9), $1M for hazmat, $5M for household goods. Require $1M minimum from all carriers regardless of commodity — the FMCSA minimum of $750K doesn't cover a serious accident. Verify insurance through the FMCSA Insurance tab, not just the certificate the carrier provides — certificates can be forged or outdated.
  - **保险最低标准**：一般货运$75万最低（按FMCSA §387.9）、危险品$100万、家用品$500万。无论货物类型，要求所有承运商至少$100万——FMCSA最低$75万不能覆盖严重事故。通过FMCSA保险标签验证保险，而不仅仅是承运商提供的证书——证书可能被伪造或过时。
- **Safety rating:** FMCSA assigns Satisfactory, Conditional, or Unsatisfactory ratings based on compliance reviews. Never use a carrier with an Unsatisfactory rating. Conditional carriers require case-by-case evaluation — understand what the conditions are. Carriers with no rating ("unrated") make up the majority — use their CSA (Compliance, Safety, Accountability) scores instead. Focus on Unsafe Driving, Hours-of-Service, and Vehicle Maintenance BASICs. A carrier in the top 25% percentile (worst) on Unsafe Driving is a liability risk.
  - **安全评级**：FMCSA根据合规审查分配满意、有条件或不满意的评级。绝不使用不满意评级的承运商。有条件承运商需要逐案评估——了解条件是什么。无评级（"未评级"）承运商占大多数——改用其CSA（合规、安全、问责）评分。关注不安全驾驶、服务时长和车辆维护BASIC。一家在不安全驾驶上处于最差25%百分位的承运商是责任风险。
- **Broker bond verification:** If using brokers, verify their $75K surety bond or trust fund is active. A broker whose bond has been revoked or reduced is likely in financial distress. Check the FMCSA Bond/Trust tab. Also verify the broker has contingent cargo insurance — this protects you if the broker's underlying carrier causes a loss and the carrier's insurance is insufficient.
  - **经纪人保证金验证**：如果使用经纪人，验证其$75K担保债券或信托基金是否有效。债券被撤销或减少的经纪人可能处于财务困境。检查FMCSA债券/信托标签。同时验证经纪人是否有应急货物保险——如果经纪人的下游承运商造成损失且承运商保险不足，这将保护您。

## Decision Frameworks
## 决策框架

### Carrier Selection for New Lanes
### 新专线承运商选择

When adding a new lane to your network, evaluate candidates on this decision tree:
当向您的网络添加新专线时，按此决策树评估候选承运商：

1. **Do existing portfolio carriers cover this lane?** If yes, negotiate with incumbents first — adding a new carrier for one lane introduces onboarding cost ($500-$1,500) and relationship management overhead. Offer existing carriers the new lane as incremental volume in exchange for a rate concession on an existing lane.
   - **现有组合承运商是否覆盖此专线？** 如果是，先与现有承运商谈判——为一条专线添加新承运商会引入入职成本（$500-$1,500）和关系管理开销。以新专线作为增量货量换取现有专线费率优惠。
2. **If no incumbent covers the lane:** Source 3-5 candidates. For lanes >500 miles, prioritize asset carriers with domicile within 100 miles of the origin. For lanes <300 miles, consider regional carriers and dedicated fleets. For infrequent lanes (<1 load/week), a broker with strong regional coverage may be the most practical option.
   - **如果没有现有承运商覆盖此专线**：寻找3-5个候选。对于>500英里的专线，优先选择起点100英里内有基地的资产承运商。对于<300英里的专线，考虑区域承运商和专用车队。对于不频繁的专线（<1票/周），覆盖广泛的区域经纪人可能是最实用的选择。
3. **Evaluate:** Run FMCSA compliance check. Request 12-month service history on the specific lane from each candidate (not just their network average). Check DAT lane rates for market benchmark. Compare total cost (linehaul + FSC + expected accessorials), not just linehaul.
   - **评估**：运行FMCSA合规检查。从每个候选请求特定专线12个月服务历史（不仅仅是他们网络平均）。检查DAT专线费率作为市场基准。比较总成本（里程费 + FSC + 预期附加费），而不仅仅是里程费。
4. **Trial period:** Award 30-day trial at contracted rates. Set clear KPIs: OTD ≥93%, tender acceptance ≥85%, invoice accuracy ≥95%. Review at 30 days — do not lock in a 12-month commitment without operational validation.
   - **试用期**：按合同费率给予30天试用。设定明确的KPI：OTD ≥93%、托运接受率 ≥85%、发票准确率 ≥95%。30天时审查——未经运营验证不要锁定12个月承诺。

### When to Consolidate vs. Diversify
### 何时整合 vs. 何时多元化

- **Consolidate (reduce carrier count) when:** You have more than 3 carriers on a lane with <5 loads/week (each carrier gets too little volume to care). Your carrier management resources are stretched. You need deeper pricing from a strategic partner (volume concentration = leverage). The market is loose and carriers are competing for your freight.
  - **整合（减少承运商数量）当**：一条专线每周<5票货但有超过3家承运商（每家承运商获得的量级太少不值得重视）。您的承运商管理资源紧张。您需要从战略合作伙伴获得更深入的定价（量级集中 = 杠杆）。市场疲软且承运商在争夺您的货物。
- **Diversify (add carriers) when:** A single carrier handles >40% of a critical lane. Tender rejections are rising above 15% on a lane. You're entering peak season and need surge capacity. A carrier shows financial distress indicators (late payments to drivers reported on Carrier411, FMCSA insurance lapses, sudden driver turnover visible via CDL postings).
  - **多元化（添加承运商）当**：一家承运商处理关键专线>40%的货物。一条专线托运拒绝率升至15%以上。您进入旺季需要峰值运力。承运商显示财务困境迹象（Carrier411报告向驾驶员付款延迟、FMCSA保险失效、 CDL帖子可见驾驶员突然流失）。

### Spot vs. Contract Decisions
### 现货 vs. 合同决策

- **Stay on contract when:** The spread between contract and spot is <10%. You have consistent, predictable volume. Capacity is tightening (spot rates are rising). The lane is customer-critical with tight delivery windows.
  - **保持合同当**：合同价与现货价差距<10%。您有持续、可预测的量级。运力正在收紧（现货价在上升）。该专线对客户至关重要且交付窗口紧凑。
- **Go to spot when:** Spot rates are >15% below your contract rate (market is soft). The lane is irregular (<1 load/week). You need one-time surge capacity beyond your routing guide. Your contract carrier is consistently rejecting tenders on this lane (they're effectively pricing you into spot anyway).
  - **使用现货当**：现货价低于合同价>15%（市场疲软）。该专线不规则（<1票/周）。您需要超出路线指南的一次性峰值运力。您的合同承运商在该专线持续拒绝托运（他们实际上在把您推向现货市场）。
- **Renegotiate contract when:** The spread between your contract rate and DAT benchmark exceeds 15% for 60+ consecutive days. A carrier's tender acceptance drops below 75% for 30 days. You've had a significant volume change (up or down) that changes the lane economics.
  - **重新谈判合同当**：您的合同价与DAT基准差距连续60天以上超过15%。承运商托运接受率连续30天低于75%。您有重大量级变化（增减）改变了专线经济性。

### Carrier Exit Criteria
### 承运商退出标准

Remove a carrier from your active routing guide when any of these thresholds are met, after documented corrective action has failed:
在记录在案的纠正行动失败后，当达到以下任何阈值时，将承运商从活跃路线指南中移除：

- OTD below 85% for 60 consecutive days
  - 连续60天OTD低于85%
- Tender acceptance below 70% for 30 consecutive days with no communication
  - 连续30天托运接受率低于70%且无沟通
- Claims ratio exceeds 2% of spend for 90 days
  - 连续90天索赔率超过支出的2%
- FMCSA authority revoked, insurance lapsed, or safety rating downgraded to Unsatisfactory
  - FMCSA资质被撤销、保险失效或安全评级降为不满意
- Invoice accuracy below 88% for 90 days after corrective notice
  - 纠正通知后连续90天发票准确率低于88%
- Discovery of double-brokering your freight
  - 发现您的货物被双重经纪
- Evidence of financial distress: bond revocation, driver complaints on CarrierOK or Carrier411, unexplained service collapse
  - 财务困境证据：债券撤销、CarrierOK或Carrier411上的驾驶员投诉、无法解释的服务崩溃

## Key Edge Cases
## 关键边缘案例

These are situations where standard playbook decisions lead to poor outcomes. Brief summaries are included here so you can expand them into project-specific playbooks if needed.
这些是标准 playbook 决策导致不良结果的情况。这里包含简要摘要，以便您可以将它们扩展为项目特定的 playbook。

1. **Capacity squeeze during a hurricane:** Your top carrier evacuates drivers from the Gulf Coast. Spot rates triple. The temptation is to pay any rate to move freight. The expert move: activate pre-positioned regional carriers, reroute through unaffected corridors, and negotiate multi-load commitments with spot carriers to lock a rate ceiling.
   - **飓风期间的运力紧缩**：您的顶级承运商从墨西哥湾沿岸撤离驾驶员。现货价翻三倍。诱惑是支付任何费率来运送货物。专家做法：激活预先部署的区域承运商、通过未受影响走廊重新路由，并与现货承运商谈判多票货承诺以锁定费率上限。
2. **Double-brokering discovery:** You're told the truck that arrived isn't from the carrier on your BOL. The insurance chain may be broken and your freight is at higher risk. Do not accept the load if it hasn't departed. If in transit, document everything and demand a written explanation within 24 hours.
   - **发现双重经纪**：您被告知到达的卡车不是您BOL上的承运商。保险链可能已断裂且您的货物处于更高风险中。如果货物未出发不要接收。如果在运输中，记录一切并在24小时内要求书面解释。
3. **Rate renegotiation after 40% volume loss:** Your company lost a major customer and your freight volume dropped. Your carriers' contract rates were predicated on volume commitments you can no longer meet. Proactive renegotiation preserves relationships; letting carriers discover the shortfall at invoice time destroys trust.
   - **40%货量损失后费率重新谈判**：您的公司失去了一个大客户，货量下降。您的承运商合同费率基于您无法再满足的量级承诺。主动重新谈判保持关系；让承运商在发票时才发现缺口会摧毁信任。
4. **Carrier financial distress indicators:** The warning signs appear months before a carrier fails: delayed driver settlements, FMCSA insurance filings changing underwriters frequently, bond amount dropping, Carrier411 complaints spiking. Reduce exposure incrementally — don't wait for the failure.
   - **承运商财务困境迹象**：警告信号在承运商倒闭前数月出现：驾驶员结算延迟、FMCSA保险申报频繁更换承保人、债券金额下降、Carrier411投诉激增。逐步减少敞口——不要等到倒闭。
5. **Mega-carrier acquisition of your niche partner:** Your best regional carrier just got acquired by a national fleet. Expect service disruption during integration, rate renegotiation attempts, and potential loss of your dedicated account manager. Secure alternative capacity before the transition completes.
   - **大型承运商收购您的利基合作伙伴**：您最好的区域承运商刚被全国性车队收购。预期整合期间服务中断、费率重新谈判尝试，以及可能失去您的专属客户经理。在过渡完成前确保替代运力。
6. **Fuel surcharge manipulation:** A carrier proposes an artificially low base rate with an aggressive FSC schedule that inflates the total cost above market. Always model total cost across a range of diesel prices ($3.50, $4.00, $4.50/gal) to expose this tactic.
   - **燃油附加费操纵**：承运商提出人为压低的基本费率配合激进的FSC表，使总成本高于市场。始终在不同柴油价格范围（$3.50、$4.00、$4.50/加仑）建模总成本以揭穿此策略。
7. **Detention and accessorial disputes at scale:** When detention charges represent >5% of a carrier's total billing, the root cause is usually shipper facility operations, not carrier overcharging. Address the operational issue before disputing the charges — or lose the carrier.
   - **大规模滞留和附加费争议**：当滞留费占承运商总账单>5%时，根本原因通常是发货人设施运营，而不是承运商过度收费。在争议费用之前先解决运营问题——否则会失去承运商。

## Communication Patterns
## 沟通模式

### Rate Negotiation Tone
### 费率谈判语调

Rate negotiations are long-term relationship conversations, not one-time transactions. Calibrate tone:
费率谈判是长期关系对话，不是一次性交易。调整语调：

- **Opening position:** Lead with data, not demands. "DAT shows this lane averaging $2.15/mile over the last 90 days. Our current contract is $2.45. We'd like to discuss alignment." Never say "your rate is too high" — say "the market has shifted and we want to make sure we're in a competitive position together."
  - **开场立场**：以数据开场，而非要求。"DAT显示该专线过去90天平均$2.15/英里。我们当前合同是$2.45。我们希望讨论对齐。" 永远不要说"您的费率太高"——而是说"市场已经变化，我们希望确保我们处于有竞争力的位置。"
- **Counter-offers:** Acknowledge the carrier's perspective. "We understand driver pay increases are real. Let's find a number that keeps this lane attractive for your drivers while keeping us competitive." Meet in the middle on base rate, negotiate harder on accessorials and FSC table.
  - **还价**：认可承运商的立场。"我们理解驾驶员薪酬上涨是真实的。让我们找到一个让这条专线对您的驾驶员有吸引力同时保持我们竞争力的数字。" 在基本费率上取中间值，在附加费和FSC表上更艰难谈判。
- **Annual reviews:** Frame as partnership check-ins, not cost-cutting exercises. Share your volume forecast, growth plans, and lane changes. Ask what you can do operationally to help the carrier (faster dock times, consistent scheduling, drop-trailer programs). Carriers give better rates to shippers who make their drivers' lives easier.
  - **年度审查**：框架为伙伴关系检查，而非削减成本练习。分享您的量级预测、增长计划和专线变化。询问您可以在运营上如何帮助承运商（更快的装卸时间、一致的调度、甩挂计划）。承运商给让驾驶员生活更轻松的发货人更好的费率。

### Performance Reviews
### 绩效审查

- **Positive reviews:** Be specific. "Your 97% OTD on the Chicago–Dallas lane saved us approximately $45K in expedite costs this quarter. We're increasing your allocation from 60% to 75% on that lane." Carriers invest in relationships that reward performance.
  - **正面审查**：要具体。"您芝加哥-达拉斯专线97%的OTD本季度为我们节省了约$45K的加急成本。我们将那条专线的分配从60%增加到75%。" 承运商投资于奖励表现的关系。
- **Corrective reviews:** Lead with data, not accusations. Present the scorecard. Identify the specific metrics below threshold. Ask for a corrective action plan with a 30/60/90-day timeline. Set a clear consequence: "If OTD on this lane doesn't reach 92% by the 60-day mark, we'll need to shift 50% of volume to an alternate carrier."
  - **纠正审查**：以数据开场，而非指责。展示计分卡。识别低于阈值的具体指标。要求提供30/60/90天时间线的纠正行动计划。设定明确后果："如果这条专线的OTD在60天时未达到92%，我们将需要将50%量级转移给替代承运商。"

Use the review patterns above as a base and adapt the language to your carrier contracts, escalation paths, and customer commitments.
以上审查模式作为基础，并根据您的承运商合同、升级路径和客户承诺调整措辞。

## Escalation Protocols
## 升级协议

### Automatic Escalation Triggers
### 自动升级触发器

| Trigger | Action | Timeline |
| 触发器 | 行动 | 时间线 |
|---|---|---|
| Carrier tender acceptance drops below 70% for 2 consecutive weeks | Notify procurement, schedule carrier call | Within 48 hours |
| 承运商托运接受率连续2周低于70% | 通知采购，安排与承运商通话 | 48小时内 |
| Spot spend exceeds 30% of lane budget for any lane | Review routing guide, initiate carrier sourcing | Within 1 week |
| 任何专线的现货支出超过专线预算30% | 审查路线指南，启动承运商寻源 | 1周内 |
| Carrier FMCSA authority or insurance lapses | Immediately suspend tendering, notify operations | Within 1 hour |
| 承运商FMCSA资质或保险失效 | 立即暂停托运，通知运营 | 1小时内 |
| Single carrier controls >50% of a critical lane | Initiate secondary carrier qualification | Within 2 weeks |
| 单个承运商控制关键专线>50% | 启动次级承运商资质认证 | 2周内 |
| Claims ratio exceeds 1.5% for any carrier for 60+ days | Schedule formal performance review | Within 1 week |
| 任何承运商索赔率连续60天以上超过1.5% | 安排正式绩效审查 | 1周内 |
| Rate variance >20% from DAT benchmark on 5+ lanes | Initiate contract renegotiation or mini-bid | Within 2 weeks |
| 5条以上专线费率偏离DAT基准>20% | 启动合同重新谈判或小型竞标 | 2周内 |
| Carrier reports driver shortage or service disruption | Activate backup carriers, increase monitoring | Within 4 hours |
| 承运商报告驾驶员短缺或服务中断 | 激活备用承运商，加强监控 | 4小时内 |
| Double-brokering confirmed on any load | Immediate carrier suspension, compliance review | Within 2 hours |
| 任何货物上确认双重经纪 | 立即暂停承运商，合规审查 | 2小时内 |

### Escalation Chain
### 升级链

Analyst → Transportation Manager (48 hours) → Director of Transportation (1 week) → VP Supply Chain (persistent issue or >$100K exposure)
分析师 → 运输经理（48小时）→ 运输总监（1周）→ 供应链副总裁（持续问题或>$10万敞口）

## Performance Indicators
## 绩效指标

Track weekly, review monthly with carrier management team, share quarterly with carriers:
每周跟踪，与承运商管理团队每月审查，每季度与承运商分享：

| Metric | Target | Red Flag |
| 指标 | 目标 | 红色警报 |
|---|---|---|
| Contract rate vs. DAT benchmark | Within ±8% | >15% premium or discount |
| 合同价与DAT基准 | ±8%以内 | >15%溢价或折扣 |
| Routing guide compliance (% of freight on guide) | ≥85% | <70% |
| 路线指南合规性（指南上的货运百分比） | ≥85% | <70% |
| Primary tender acceptance | ≥90% | <80% |
| 主承运商托运接受率 | ≥90% | <80% |
| Weighted average OTD across portfolio | ≥95% | <90% |
| 组合加权平均OTD | ≥95% | <90% |
| Carrier portfolio claims ratio | <0.5% of spend | >1.0% |
| 承运商组合索赔率 | <支出的0.5% | >1.0% |
| Average carrier invoice accuracy | ≥97% | <93% |
| 平均承运商发票准确率 | ≥97% | <93% |
| Spot freight percentage | <20% | >30% |
| 现货货运百分比 | <20% | >30% |
| RFP cycle time (launch to implementation) | ≤12 weeks | >16 weeks |
| RFP周期（启动到实施） | ≤12周 | >16周 |

## Additional Resources
## 额外资源

- Track carrier scorecards, exception trends, and routing-guide compliance in the same operating review so pricing and service decisions stay tied together.
  - 在同一运营审查中跟踪承运商计分卡、异常趋势和路线指南合规性，使定价和服务决策保持关联。
- Capture your organization's preferred negotiation positions, accessorial guardrails, and escalation triggers alongside this skill before using it in production.
  - 在生产使用此技能之前，捕获您组织首选的谈判立场、附加费护栏和升级触发器。
