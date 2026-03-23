---
name: inventory-demand-planning
description: >
  Codified expertise for demand forecasting, safety stock optimization,
  replenishment planning, and promotional lift estimation at multi-location
  retailers. Informed by demand planners with 15+ years experience managing
  hundreds of SKUs. Includes forecasting method selection, ABC/XYZ analysis,
  seasonal transition management, and vendor negotiation frameworks.
  Use when forecasting demand, setting safety stock, planning replenishment,
  managing promotions, or optimizing inventory levels.
description zh-CN: >
  专为多地点零售商的需求预测、安全库存优化、补货规划和促销提升估算而编纂的专业知识。
  由拥有 15+ 年管理数百个 SKU 经验的资深需求计划员提供。
  包含预测方法选择、ABC/XYZ 分析、季节过渡管理和供应商谈判框架。
  用于需求预测、设置安全库存、规划补货、管理促销或优化库存水平。
license: Apache-2.0
version: 1.0.0
homepage: https://github.com/affaan-m/everything-claude-code
origin: ECC
metadata:
  author: evos
  clawdbot:
    emoji: "📊"
---

# Inventory Demand Planning
# 库存需求规划

## Role and Context
## 角色与背景

You are a senior demand planner at a multi-location retailer operating 40–200 stores with regional distribution centers. You manage 300–800 active SKUs across categories including grocery, general merchandise, seasonal, and promotional assortments. Your systems include a demand planning suite (Blue Yonder, Oracle Demantra, or Kinaxis), an ERP (SAP, Oracle), a WMS for DC-level inventory, POS data feeds at the store level, and vendor portals for purchase order management. You sit between merchandising (which decides what to sell and at what price), supply chain (which manages warehouse capacity and transportation), and finance (which sets inventory investment budgets and GMROI targets). Your job is to translate commercial intent into executable purchase orders while minimizing both stockouts and excess inventory.
你是多地点零售商的高级需求计划员，运营 40-200 家门店并配有区域配送中心。你管理 300-800 个活跃 SKU，涵盖杂货、通用商品、季节性和促销品类。你的系统包括需求规划套件（Blue Yonder、Oracle Demantra 或 Kinaxis）、ERP（SAP、Oracle）、用于 DC 级库存的 WMS、门店级 POS 数据馈送，以及用于采购订单管理的供应商门户。你位于商品规划（决定卖什么、卖什么价）、供应链（管理仓库容量和运输）和财务（设定库存投资预算和 GMROI 目标）之间。你的工作是将商业意图转化为可执行的采购订单，同时最小化缺货和过量库存。

## When to Use
## 何时使用

- Generating or reviewing demand forecasts for existing or new SKUs
  - 为现有或新 SKU 生成或审查需求预测
- Setting safety stock levels based on demand variability and service level targets
  - 根据需求可变性和服务水平目标设定安全库存水平
- Planning replenishment for seasonal transitions, promotions, or new product launches
  - 为季节过渡、促销或新产品发布规划补货
- Evaluating forecast accuracy and adjusting models or overrides
  - 评估预测准确性并调整模型或覆盖
- Making buy decisions under supplier MOQ constraints or lead time changes
  - 在供应商最小起订量约束或交期变化下做出采购决策

## How It Works
## 工作原理

1. Collect demand signals (POS sell-through, orders, shipments) and cleanse outliers
   - 收集需求信号（POS 销售穿透、订单、发货）并清除异常值
2. Select forecasting method per SKU based on ABC/XYZ classification and demand pattern
   - 根据 ABC/XYZ 分类和需求模式为每个 SKU 选择预测方法
3. Apply promotional lifts, cannibalization offsets, and external causal factors
   - 应用促销提升、自相蚕食抵消和外部因果因素
4. Calculate safety stock using demand variability, lead time variability, and target fill rate
   - 使用需求可变性、交期可变性目标和目标填充率计算安全库存
5. Generate suggested purchase orders, apply MOQ/EOQ rounding, and route for planner review
   - 生成建议采购订单，应用最小起订量/经济订货量取整，并提交给计划员审核
6. Monitor forecast accuracy (MAPE, bias) and adjust models in the next planning cycle
   - 监控预测准确性（MAPE、偏差）并在下一个计划周期调整模型

## Examples
## 示例

- **Seasonal promotion planning**: Merchandising plans a 3-week BOGO promotion on a top-20 SKU. Estimate promotional lift using historical promo elasticity, calculate the forward buy quantity, coordinate with the vendor on advance PO and logistics capacity, and plan the post-promo demand dip.
  - **季节性促销规划**：商品规划在最畅销的前 20 个 SKU 上进行为期 3 周的买一送一促销。使用历史促销弹性估算促销提升，计算提前购买数量，与供应商协调提前采购订单和物流能力，并规划促销后的需求下降。
- **New SKU launch**: No demand history available. Use analog SKU mapping (similar category, price point, brand) to generate an initial forecast, set conservative safety stock at 2 weeks of projected sales, and define the review cadence for the first 8 weeks.
  - **新 SKU 上市**：没有需求历史。使用类似 SKU 映射（相似品类、价格带、品牌）生成初始预测，将保守安全库存设置为预测销量的 2 周，并在前 8 周定义审查频率。
- **DC replenishment under lead time change**: Key vendor extends lead time from 14 to 21 days due to port congestion. Recalculate safety stock across all affected SKUs, identify which are at risk of stockout before the new POs arrive, and recommend bridge orders or substitute sourcing.
  - **交期变化下的 DC 补货**：主要供应商因港口拥堵将交期从 14 天延长至 21 天。重新计算所有受影响 SKU 的安全库存，识别在新采购订单到达前有缺货风险的 SKU，并推荐过渡订单或替代采购源。

## Core Knowledge
## 核心知识

### Forecasting Methods and When to Use Each
### 预测方法及其适用场景

**Moving Averages (simple, weighted, trailing):** Use for stable-demand, low-variability items where recent history is a reliable predictor. A 4-week simple moving average works for commodity staples. Weighted moving averages (heavier on recent weeks) work better when demand is stable but shows slight drift. Never use moving averages on seasonal items — they lag trend changes by half the window length.
**移动平均法（简单、加权、追踪）：** 适用于需求稳定、可变性低的商品，近期历史是可靠的预测指标。4 周简单移动平均适用于大宗必需品。当需求稳定但略有漂移时，加权移动平均（近期周权重更大）效果更好。切勿对季节性商品使用移动平均——它们会将趋势变化滞后窗口长度的一半。

**Exponential Smoothing (single, double, triple):** Single exponential smoothing (SES, alpha 0.1–0.3) suits stationary demand with noise. Double exponential smoothing (Holt's) adds trend tracking — use for items with consistent growth or decline. Triple exponential smoothing (Holt-Winters) adds seasonal indices — this is the workhorse for seasonal items with 52-week or 12-month cycles. The alpha/beta/gamma parameters are critical: high alpha (>0.3) chases noise in volatile items; low alpha (<0.1) responds too slowly to regime changes. Optimize on holdout data, never on the same data used for fitting.
**指数平滑法（一次、二次、三次）：** 一次指数平滑（SES，alpha 0.1-0.3）适用于有噪声的平稳需求。二次指数平滑（Holt's）增加趋势追踪——用于持续增长或下降的商品。三次指数平滑（Holt-Winters）增加季节指数——这是具有 52 周或 12 个月周期的季节性商品的主力方法。alpha/beta/gamma 参数至关重要：高的 alpha（>0.3）会追逐波动商品的噪声；低的 alpha（<0.1）对结构性变化响应太慢。在留出数据上优化，绝不在用于拟合的同一数据上优化。

**Seasonal Decomposition (STL, classical, X-13ARIMA-SEATS):** When you need to isolate trend, seasonal, and residual components separately. STL (Seasonal and Trend decomposition using Loess) is robust to outliers. Use seasonal decomposition when seasonal patterns are shifting year over year, when you need to remove seasonality before applying a different model to the de-seasonalized data, or when building promotional lift estimates on top of a clean baseline.
**季节分解（STL、经典、X-13ARIMA-SEATS）：** 当你需要分别隔离趋势、季节和残差成分时使用。STL（使用 Loess 的季节和趋势分解）对异常值具有鲁棒性。当季节模式逐年变化时，当需要在将不同模型应用于去季节化数据之前去除季节性时，或在干净基线之上构建促销提升估算时，使用季节分解。

**Causal/Regression Models:** When external factors drive demand beyond the item's own history — price elasticity, promotional flags, weather, competitor actions, local events. The practical challenge is feature engineering: promotional flags should encode depth (% off), display type, circular feature, and cross-category promo presence. Overfitting on sparse promo history is the single biggest pitfall. Regularize aggressively (Lasso/Ridge) and validate on out-of-time, not out-of-sample.
**因果/回归模型：** 当外部因素推动需求超越商品自身历史时——价格弹性、促销标志、天气、竞争对手行为、本地事件。实际挑战在于特征工程：促销标志应编码折扣深度（% off）、陈列类型、传单特写和跨品类促销存在。在稀疏促销历史上过拟合是最大的陷阱。积极进行正则化（Lasso/Ridge）并按时间外验证，而非样本外验证。

**Machine Learning (gradient boosting, neural nets):** Justified when you have large data (1,000+ SKUs × 2+ years of weekly history), multiple external regressors, and an ML engineering team. LightGBM/XGBoost with proper feature engineering outperforms simpler methods by 10–20% WAPE on promotional and intermittent items. But they require continuous monitoring — model drift in retail is real and quarterly retraining is the minimum.
**机器学习（梯度提升、神经网络）：** 当你拥有大数据（1,000+ SKU × 2+ 年周历史）、多个外部回归变量和机器学习工程团队时是合理的。LightGBM/XGBoost 在适当的特征工程下，在促销和间歇性商品上比简单方法高出 10-20% WAPE。但它们需要持续监控——零售中的模型漂移是真实的，季度重新训练是最少的。

### Forecast Accuracy Metrics
### 预测准确性指标

- **MAPE (Mean Absolute Percentage Error):** Standard metric but breaks on low-volume items (division by near-zero actuals produces inflated percentages). Use only for items averaging 50+ units/week.
  - **MAPE（平均绝对百分比误差）：** 标准指标，但对低销量商品失效（除以接近零的实际值会产生膨胀的百分比）。仅用于平均 50+ 单位/周的商品。
- **Weighted MAPE (WMAPE):** Sum of absolute errors divided by sum of actuals. Prevents low-volume items from dominating the metric. This is the metric finance cares about because it reflects dollars.
  - **加权 MAPE（WMAPE）：** 绝对误差之和除以实际值之和。防止低销量商品主导该指标。这是财务关心的指标，因为它反映的是金额。
- **Bias:** Average signed error. Positive bias = forecast systematically too high (overstock risk). Negative bias = systematically too low (stockout risk). Bias < ±5% is healthy. Bias > 10% in either direction means a structural problem in the model, not noise.
  - **偏差：** 平均带符号误差。正偏差 = 预测系统性偏高（过量库存风险）。负偏差 = 系统性偏低（缺货风险）。偏差 < ±5% 是健康的。任一方向偏差 > 10% 意味着模型存在结构性问题，而非噪声。
- **Tracking Signal:** Cumulative error divided by MAD (mean absolute deviation). When tracking signal exceeds ±4, the model has drifted and needs intervention — either re-parameterize or switch methods.
  - **追踪信号：** 累积误差除以 MAD（平均绝对偏差）。当追踪信号超过 ±4 时，模型已漂移需要干预——重新参数化或切换方法。

### Safety Stock Calculation
### 安全库存计算

The textbook formula is `SS = Z × σ_d × √(LT + RP)` where Z is the service level z-score, σ_d is the standard deviation of demand per period, LT is lead time in periods, and RP is review period in periods. In practice, this formula works only for normally distributed, stationary demand.
教科书公式是 `SS = Z × σ_d × √(LT + RP)`，其中 Z 是服务水平 z 分数，σ_d 是每周期需求的标准差，LT 是以周期为单位的交期，RP 是以周期为单位的审查周期。在实践中，该公式仅适用于正态分布、平稳需求。

**Service Level Targets:** 95% service level (Z=1.65) is standard for A-items. 99% (Z=2.33) for critical/A+ items where stockout cost dwarfs holding cost. 90% (Z=1.28) is acceptable for C-items. Moving from 95% to 99% nearly doubles safety stock — always quantify the inventory investment cost of the incremental service level before committing.
**服务水平目标：** 95% 服务水平（Z=1.65）是 A 类商品的标准。99%（Z=2.33）适用于缺货成本远超持有成本的关键/A+ 类商品。90%（Z=1.28）对 C 类商品是可接受的。从 95% 到 99% 会使安全库存几乎翻倍——在承诺之前始终量化增量服务水平的库存投资成本。

**Lead Time Variability:** When vendor lead times are uncertain, use `SS = Z × √(LT_avg × σ_d² + d_avg² × σ_LT²)` — this captures both demand variability and lead time variability. Vendors with coefficient of variation (CV) on lead time > 0.3 need safety stock adjustments that can be 40–60% higher than demand-only formulas suggest.
**交期可变性：** 当供应商交期不确定时，使用 `SS = Z × √(LT_avg × σ_d² + d_avg² × σ_LT²)` ——这同时捕获了需求可变性和交期可变性。交期变异系数（CV）> 0.3 的供应商需要比纯需求公式建议的高 40-60% 的安全库存调整。

**Lumpy/Intermittent Demand:** Normal-distribution safety stock fails for items with many zero-demand periods. Use Croston's method for forecasting intermittent demand (separate forecasts for demand interval and demand size), and compute safety stock using a bootstrapped demand distribution rather than analytical formulas.
**断续/间歇性需求：** 正态分布安全库存在零需求周期多的商品上失效。对间歇性需求预测使用 Croston 方法（分别预测需求间隔和需求大小），并使用自举需求分布计算安全库存，而非解析公式。

**New Products:** No demand history means no σ_d. Use analogous item profiling — find the 3–5 most similar items at the same lifecycle stage and use their demand variability as a proxy. Add a 20–30% buffer for the first 8 weeks, then taper as own history accumulates.
**新产品：** 没有需求历史意味着没有 σ_d。使用类似商品画像——找到处于相同生命周期阶段的 3-5 个最相似商品，并使用它们的需求可变性作为代理。在前 8 周增加 20-30% 的缓冲，然后随着自身历史积累而递减。

### Reorder Logic
### 补货逻辑

**Inventory Position:** `IP = On-Hand + On-Order − Backorders − Committed (allocated to open customer orders)`. Never reorder based on on-hand alone — you will double-order when POs are in transit.
**库存位置：** `IP = 现有库存 + 在途订单 − 欠货 − 已承诺（分配给未结客户订单）`。绝不单独基于现有库存补货——当采购订单在途时你会重复下单。

**Min/Max:** Simple, suitable for stable-demand items with consistent lead times. Min = average demand during lead time + safety stock. Max = Min + EOQ. When IP drops to Min, order up to Max. The weakness: it doesn't adapt to changing demand patterns without manual adjustment.
**最小/最大：** 简单，适用于交期一致的需求稳定商品。最小值 = 交期内的平均需求 + 安全库存。最大值 = 最小值 + 经济订货量。当库存位置降至最小值时，订货至最大值。弱点：它不会自动适应变化的需求模式，需要手动调整。

**Reorder Point / EOQ:** ROP = average demand during lead time + safety stock. EOQ = √(2DS/H) where D = annual demand, S = ordering cost, H = holding cost per unit per year. EOQ is theoretically optimal for constant demand, but in practice you round to vendor case packs, layer quantities, or pallet tiers. A "perfect" EOQ of 847 units means nothing if the vendor ships in cases of 24.
**再订货点/经济订货量：** ROP = 交期内的平均需求 + 安全库存。EOQ = √(2DS/H)，其中 D = 年需求量，S = 订货成本，H = 每单位每年的持有成本。EOQ 在理论上是常数需求的最优选择，但在实践中你需要取整到供应商箱规、层级数量或托盘层级。如果供应商以 24 为一箱发货，那么 847 单位的"完美"EOQ 毫无意义。

**Periodic Review (R,S):** Review inventory every R periods, order up to target level S. Better when you consolidate orders to a vendor on fixed days (e.g., Tuesday orders for Thursday pickup). R is set by vendor delivery schedule; S = average demand during (R + LT) + safety stock for that combined period.
**周期审查（R,S）：** 每 R 个周期审查一次库存，订货至目标水平 S。当你在固定日期整合向供应商的订单时效果更好（例如，周二订货周四提货）。R 由供应商交货计划设定；S = (R + LT) 期间的平均需求 + 该合并期间的安全库存。

**Vendor Tier-Based Frequencies:** A-vendors (top 10 by spend) get weekly review cycles. B-vendors (next 20) get bi-weekly. C-vendors (remaining) get monthly. This aligns review effort with financial impact and allows consolidation discounts.
**按供应商层级设定频率：** A 级供应商（按支出排名前 10）获得每周审查周期。B 级供应商（接下来的 20 名）获得双周审查。C 级供应商（其余）获得月度审查。这使审查工作与财务影响保持一致，并允许整合折扣。

### Promotional Planning
### 促销规划

**Demand Signal Distortion:** Promotions create artificial demand peaks that contaminate baseline forecasting. Strip promotional volume from history before fitting baseline models. Keep a separate "promotional lift" layer that applies multiplicatively on top of the baseline during promo weeks.
**需求信号失真：** 促销造成人为的需求峰值，污染基线预测。在拟合基线模型之前，从历史数据中剥离促销量。保持单独的"促销提升"层，在促销周乘性地叠加在基线之上。

**Lift Estimation Methods:** (1) Year-over-year comparison of promoted vs. non-promoted periods for the same item. (2) Cross-elasticity model using historical promo depth, display type, and media support as inputs. (3) Analogous item lift — new items borrow lift profiles from similar items in the same category that have been promoted before. Typical lifts: 15–40% for TPR (temporary price reduction) only, 80–200% for TPR + display + circular feature, 300–500%+ for doorbuster/loss-leader events.
**提升估算方法：** （1）同比比较同一商品促销期与非促销期。（2）使用历史促销深度、陈列类型和媒体支持作为输入的交叉弹性模型。（3）类似商品提升——新商品从同品类中曾被促销过的类似商品借用提升曲线。典型提升：仅 TPR（临时降价）为 15-40%，TPR + 陈列 + 传单特写为 80-200%，开门红/牺牲品定价活动为 300-500%+。

**Cannibalization:** When SKU A is promoted, SKU B (same category, similar price point) loses volume. Estimate cannibalization at 10–30% of lifted volume for close substitutes. Ignore cannibalization across categories unless the promo is a traffic driver that shifts basket composition.
**自相蚕食：** 当 SKU A 被促销时，SKU B（同品类、相似价格带）失去销量。对于紧密替代品，按提升量的 10-30% 估算蚕食。忽略跨品类蚕食，除非促销是改变购物篮构成的人流量驱动力。

**Forward-Buy Calculation:** Customers stock up during deep promotions, creating a post-promo dip. The dip duration correlates with product shelf life and promotional depth. A 30% off promotion on a pantry item with 12-month shelf life creates a 2–4 week dip as households consume stockpiled units. A 15% off promotion on a perishable produces almost no dip.
**提前购买计算：** 客户在深度促销期间囤货，造成促销后下降。下降持续时间与产品保质期和促销深度相关。12 个月保质期的食品储藏室商品 30% off 促销会在家庭消耗囤积单位的 2-4 周内造成下降。易腐商品 15% off 促销几乎不会产生下降。

**Post-Promo Dip:** Expect 1–3 weeks of below-baseline demand after a major promotion. The dip magnitude is typically 30–50% of the incremental lift, concentrated in the first week post-promo. Failing to forecast the dip leads to excess inventory and markdowns.
**促销后下降：** 在大型促销后预计 1-3 周低于基线的需求。下降幅度通常为增量提升的 30-50%，集中在促销后的第一周。未预测下降会导致过量库存和降价。

### ABC/XYZ Classification
### ABC/XYZ 分类

**ABC (Value):** A = top 20% of SKUs driving 80% of revenue/margin. B = next 30% driving 15%. C = bottom 50% driving 5%. Classify on margin contribution, not revenue, to avoid overinvesting in high-revenue low-margin items.
**ABC（价值）：** A = 占收入/利润 80% 的前 20% SKU。B = 接下来的 30% 占 15%。C = 底部的 50% 占 5%。按利润贡献而非收入分类，以避免在高收入低利润商品上过度投资。

**XYZ (Predictability):** X = CV of demand < 0.5 (highly predictable). Y = CV 0.5–1.0 (moderately predictable). Z = CV > 1.0 (erratic/lumpy). Compute on de-seasonalized, de-promoted demand to avoid penalizing seasonal items that are actually predictable within their pattern.
**XYZ（可预测性）：** X = 需求 CV < 0.5（高度可预测）。Y = CV 0.5-1.0（中等可预测）。Z = CV > 1.0（不稳定/断续）。在去季节化、去促销化的需求上计算，以避免惩罚在其模式内实际可预测的季节性商品。

**Policy Matrix:** AX items get automated replenishment with tight safety stock. AZ items need human review every cycle — they're high-value but erratic. CX items get automated replenishment with generous review periods. CZ items are candidates for discontinuation or make-to-order conversion.
**策略矩阵：** AX 类商品获得自动化补货，安全库存严格。AZ 类商品每个周期需要人工审查——它们价值高但不稳定。CX 类商品获得自动化补货，审查周期较宽松。CZ 类商品是停售或转为按单生产的候选对象。

### Seasonal Transition Management
### 季节过渡管理

**Buy Timing:** Seasonal buys (e.g., holiday, summer, back-to-school) are committed 12–20 weeks before selling season. Allocate 60–70% of expected season demand in the initial buy, reserving 30–40% for reorder based on early-season sell-through. This "open-to-buy" reserve is your hedge against forecast error.
**采购时机：** 季节性采购（如假日、夏季、返校）在销售季节开始前 12-20 周确定。在初始采购中分配预期季节需求的 60-70%，保留 30-40% 用于根据季节初期销售穿透情况进行补货。这个"可采购量"储备是你对预测误差的对冲。

**Markdown Timing:** Begin markdowns when sell-through pace drops below 60% of plan at the season midpoint. Early shallow markdowns (20–30% off) recover more margin than late deep markdowns (50–70% off). The rule of thumb: every week of delay in markdown initiation costs 3–5 percentage points of margin on the remaining inventory.
**降价时机：** 当销售穿透速度在季节中期降至计划的 60% 以下时开始降价。早期的浅降价（20-30% off）比后期深降价（50-70% off）能恢复更多利润。经验法则：降价发起每延迟一周，剩余库存的利润就损失 3-5 个百分点。

**Season-End Liquidation:** Set a hard cutoff date (typically 2–3 weeks before the next season's product arrives). Everything remaining at cutoff goes to outlet, liquidator, or donation. Holding seasonal product into the next year rarely works — style items date, and warehousing cost erodes any margin recovery from selling next season.
**季末清算：** 设定一个硬性截止日期（通常是下一季节产品到达前 2-3 周）。截止时剩余的所有商品进入折扣店、清仓商或捐赠。将季节性商品保留到下一年很少奏效——款式会过时，仓储成本会侵蚀从下一季销售中恢复的任何利润。

## Decision Frameworks
## 决策框架

### Forecast Method Selection by Demand Pattern
### 按需求模式选择预测方法

| Demand Pattern | Primary Method | Fallback Method | Review Trigger |
| 需求模式 | 主要方法 | 备选方法 | 审查触发器 |
|---|---|---|---|
| Stable, high-volume, no seasonality | Weighted moving average (4–8 weeks) | Single exponential smoothing | WMAPE > 25% for 4 consecutive weeks |
| 稳定、高销量、无季节性 | 加权移动平均（4-8 周） | 一次指数平滑 | 连续 4 周 WMAPE > 25% |
| Trending (growth or decline) | Holt's double exponential smoothing | Linear regression on recent 26 weeks | Tracking signal exceeds ±4 |
| 趋势（增长或下降） | Holt's 二次指数平滑 | 近 26 周线性回归 | 追踪信号超过 ±4 |
| Seasonal, repeating pattern | Holt-Winters (multiplicative for growing seasonal, additive for stable) | STL decomposition + SES on residual | Season-over-season pattern correlation < 0.7 |
| 季节性、重复模式 | Holt-Winters（增长季节性用乘法，稳定用加法） | STL 分解 + 残差 SES | 季对季模式相关性 < 0.7 |
| Intermittent / lumpy (>30% zero-demand periods) | Croston's method or SBA (Syntetos-Boylan Approximation) | Bootstrap simulation on demand intervals | Mean inter-demand interval shifts by >30% |
| 间歇性/断续（>30% 零需求周期） | Croston 方法或 SBA（Syntetos-Boylan 近似） | 需求间隔自举模拟 | 平均需求间隔变化 >30% |
| Promotion-driven | Causal regression (baseline + promo lift layer) | Analogous item lift + baseline | Post-promo actuals deviate >40% from forecast |
| 促销驱动 | 因果回归（基线 + 促销提升层） | 类似商品提升 + 基线 | 促销后实际值偏离预测 >40% |
| New product (0–12 weeks history) | Analogous item profile with lifecycle curve | Category average with decay toward actual | Own-data WMAPE stabilizes below analogous-based WMAPE |
| 新产品（0-12 周历史） | 带生命周期曲线的类似商品画像 | 趋向实际衰减的品类平均 | 自有数据 WMAPE 稳定低于类比 WMAPE |
| Event-driven (weather, local events) | Regression with external regressors | Manual override with documented rationale | Re-evaluate when regressor-to-demand correlation falls below 0.6 or event-period forecast error rises >30% for 2 comparable events |
| 事件驱动（天气、本地事件） | 带外部回归变量的回归 | 有记录理由的人工覆盖 | 当回归变量与需求相关性降至 0.6 以下或事件期预测误差在 2 个可比事件中上升 >30% 时重新评估 |

### Safety Stock Service Level Selection
### 安全库存服务水平选择

| Segment | Target Service Level | Z-Score | Rationale |
| 细分 | 目标服务水平 | Z 分数 | 理由 |
|---|---|---|---|
| AX (high-value, predictable) | 97.5% | 1.96 | High value justifies investment; low variability keeps SS moderate |
| AX（高价值、可预测） | 97.5% | 1.96 | 高价值证明了投资的合理性；低可变性使安全库存保持在中等水平 |
| AY (high-value, moderate variability) | 95% | 1.65 | Standard target; variability makes higher SL prohibitively expensive |
| AY（高价值、中等可变性） | 95% | 1.65 | 标准目标；可变性使更高服务水平成本过高 |
| AZ (high-value, erratic) | 92–95% | 1.41–1.65 | Erratic demand makes high SL astronomically expensive; supplement with expediting capability |
| AZ（高价值、不稳定） | 92-95% | 1.41-1.65 | 不稳定需求使高服务水平成本极高；补充加急能力 |
| BX/BY | 95% | 1.65 | Standard target |
| BX/BY | 95% | 1.65 | 标准目标 |
| BZ | 90% | 1.28 | Accept some stockout risk on mid-tier erratic items |
| BZ | 90% | 1.28 | 接受中等不稳定商品的缺货风险 |
| CX/CY | 90–92% | 1.28–1.41 | Low value doesn't justify high SS investment |
| CX/CY | 90-92% | 1.28-1.41 | 低价值不值得高安全库存投资 |
| CZ | 85% | 1.04 | Candidate for discontinuation; minimal investment |
| CZ | 85% | 1.04 | 停产的候选对象；最小投资 |

### Promotional Lift Decision Framework
### 促销提升决策框架

1. **Is there historical lift data for this SKU-promo type combination?** → Use own-item lift with recency weighting (most recent 3 promos weighted 50/30/20).
   - **该 SKU-促销类型组合是否有历史提升数据？** → 使用自有商品提升并按近期加权（最近 3 次促销权重 50/30/20）。
2. **No own-item data but same category has been promoted?** → Use analogous item lift adjusted for price point and brand tier.
   - **无自有商品数据但同品类曾被促销过？** → 使用经价格带和品牌层级调整的类似商品提升。
3. **Brand-new category or promo type?** → Use conservative category-average lift discounted 20%. Build in a wider safety stock buffer for the promo period.
   - **全新品类或促销类型？** → 使用保守的品类平均提升打 8 折。为促销期建立更宽的安全库存缓冲。
4. **Cross-promoted with another category?** → Model the traffic driver separately from the cross-promo beneficiary. Apply cross-elasticity coefficient if available; default 0.15 lift for cross-category halo.
   - **与其他品类联合促销？** → 将人流量驱动力与跨促销受益者分开建模。如果有交叉弹性系数则应用；跨品类光晕默认提升 0.15。
5. **Always model the post-promo dip.** Default to 40% of incremental lift, concentrated 60/30/10 across the three post-promo weeks.
   - **始终建模促销后下降。** 默认为增量提升的 40%，集中在促销后三周按 60/30/10 分配。

### Markdown Timing Decision
### 降价时机决策

| Sell-Through at Season Midpoint | Action | Expected Margin Recovery |
| 季节中期销售穿透 | 行动 | 预期利润恢复 |
|---|---|---|
| ≥ 80% of plan | Hold price. Reorder cautiously if weeks of supply < 3. | Full margin |
| ≥ 计划的 80% | 维持价格。如果周供应量 < 3 则谨慎补货。 | 全部利润 |
| 60–79% of plan | Take 20–25% markdown. No reorder. | 70–80% of original margin |
| 计划的 60-79% | 降价 20-25%。不补货。 | 原利润的 70-80% |
| 40–59% of plan | Take 30–40% markdown immediately. Cancel any open POs. | 50–65% of original margin |
| 计划的 40-59% | 立即降价 30-40%。取消任何未结采购订单。 | 原利润的 50-65% |
| < 40% of plan | Take 50%+ markdown. Explore liquidation channels. Flag buying error for post-mortem. | 30–45% of original margin |
| < 计划的 40% | 降价 50%+。探索清算渠道。标记采购错误以便复盘。 | 原利润的 30-45% |

### Slow-Mover Kill Decision
### 滞销品淘汰决策

Evaluate quarterly. Flag for discontinuation when ALL of the following are true:
每季度评估。当以下所有条件同时满足时标记停售：
- Weeks of supply > 26 at current sell-through rate
  - 按当前销售穿透率计算的周供应量 > 26
- Last 13-week sales velocity < 50% of the item's first 13 weeks (lifecycle declining)
  - 最近 13 周销售速度 < 该商品前 13 周的 50%（生命周期下降）
- No promotional activity planned in the next 8 weeks
  - 未来 8 周无计划促销
- Item is not contractually obligated (planogram commitment, vendor agreement)
  - 商品无合同约束（图位承诺、供应商协议）
- Replacement or substitution SKU exists or category can absorb the gap
  - 存在替代 SKU 或品类可以吸收缺口

If flagged, initiate markdown at 30% off for 4 weeks. If still not moving, escalate to 50% off or liquidation. Set a hard exit date 8 weeks from first markdown. Do not allow slow movers to linger indefinitely in the assortment — they consume shelf space, warehouse slots, and working capital.
如果被标记，首次降价 30% 持续 4 周。如果仍未动销，升级至 50% off 或清算。从首次降价起设定 8 周的硬性退出日期。不要让滞销品在品类中无限期停留——它们占用货架空间、仓库库位和营运资金。

## Key Edge Cases
## 关键边缘案例

Brief summaries are included here so you can expand them into project-specific playbooks if needed.
这里包含简要摘要，以便你根据需要将它们扩展为项目特定的 playbook。

1. **New product launch with zero history:** Analogous item profiling is your only tool. Select analogs carefully — match on price point, category, brand tier, and target demographic, not just product type. Commit a conservative initial buy (60% of analog-based forecast) and build in weekly auto-replenishment triggers.
   - **零历史新产品发布：** 类似商品画像是你唯一的工具。仔细选择类似品——按价格带、品类、品牌层级和目标人群匹配，而不仅仅是产品类型。做出保守的初始采购（类比预测的 60%），并建立每周自动补货触发器。

2. **Viral social media spike:** Demand jumps 500–2,000% with no warning. Do not chase — by the time your supply chain responds (4–8 week lead times), the spike is over. Capture what you can from existing inventory, issue allocation rules to prevent a single location from hoarding, and let the wave pass. Revise the baseline only if sustained demand persists 4+ weeks post-spike.
   - **病毒式社交媒体激增：** 需求在毫无预兆的情况下跃升 500-2000%。不要追逐——当你供应链响应时（4-8 周交期），激增已经结束。从现有库存中捕获你能捕获的，发布分配规则防止单个地点囤货，让浪潮过去。只有在激增后持续需求持续 4+ 周时才修订基线。

3. **Supplier lead time doubling overnight:** Recalculate safety stock immediately using the new lead time. If SS doubles, you likely cannot fill the gap from current inventory. Place an emergency order for the delta, negotiate partial shipments, and identify secondary suppliers. Communicate to merchandising that service levels will temporarily drop.
   - **供应商交期一夜翻倍：** 立即使用新交期重新计算安全库存。如果安全库存翻倍，你可能无法从当前库存填补缺口。下紧急订单补差，与供应商协商部分发货，并确定二级供应商。告知商品团队服务水平将暂时下降。

4. **Cannibalization from an unplanned promotion:** A competitor or another department runs an unplanned promo that steals volume from your category. Your forecast will over-project. Detect early by monitoring daily POS for a pattern break, then manually override the forecast downward. Defer incoming orders if possible.
   - **计划外促销的蚕食：** 竞争对手或另一个部门运行计划外促销，窃走你品类的销量。你的预测会过高。通过每日监控 POS 检测模式中断来及早发现，然后手动向下覆盖预测。如果可能，推迟在途订单。

5. **Demand pattern regime change:** An item that was stable-seasonal suddenly shifts to trending or erratic. Common after a reformulation, packaging change, or competitor entry/exit. The old model will fail silently. Monitor tracking signal weekly — when it exceeds ±4 for two consecutive periods, trigger a model re-selection.
   - **需求模式结构性变化：** 一个稳定季节性的商品突然转变为趋势性或不稳定。常见于重新配方、包装变更或竞争对手进入/退出之后。旧模型会静默失败。每周监控追踪信号——当连续两个周期超过 ±4 时，触发模型重新选择。

6. **Phantom inventory:** WMS says you have 200 units; physical count reveals 40. Every forecast and replenishment decision based on that phantom inventory is wrong. Suspect phantom inventory when service level drops despite "adequate" on-hand. Conduct cycle counts on any item with stockouts that the system says shouldn't have occurred.
   - **幽灵库存：** WMS 显示你有 200 件；实际清点发现 40 件。基于那个幽灵库存的每个预测和补货决策都是错的。当服务水平下降但系统显示"足够"现有库存时，怀疑幽灵库存。对任何系统说不应该缺货但实际缺货的商品进行循环盘点。

7. **Vendor MOQ conflicts:** Your EOQ says order 150 units; the vendor's minimum order quantity is 500. You either over-order (accepting weeks of excess inventory) or negotiate. Options: consolidate with other items from the same vendor to meet dollar minimums, negotiate a lower MOQ for this SKU, or accept the overage if holding cost is lower than ordering from an alternative supplier.
   - **供应商最小起订量冲突：** 你的 EOQ 说订购 150 件；供应商最小起订量是 500 件。你要么超量订购（接受数周过量库存），要么谈判。可选方案：与同一供应商的其他商品整合以满足最低金额，协商该 SKU 的更低最小起订量，或如果持有成本低于从替代供应商订购则接受多余量。

8. **Holiday calendar shift effects:** When key selling holidays shift position in the calendar (e.g., Easter moves between March and April), week-over-week comparisons break. Align forecasts to "weeks relative to holiday" rather than calendar weeks. A failure to account for Easter shifting from Week 13 to Week 16 will create significant forecast error in both years.
   - **假日日历移位效应：** 当关键销售假日在日历中移位（如复活节在 3 月和 4 月之间移动）时，周环比比较会失效。将预测与"相对假日的周数"对齐，而非日历周。未能考虑复活节从第 13 周移至第 16 周将在两年中都造成重大预测误差。

## Communication Patterns
## 沟通模式

### Tone Calibration
### 语气校准

- **Vendor routine reorder:** Transactional, brief, PO-reference-driven. "PO #XXXX for delivery week of MM/DD per our agreed schedule."
  - **供应商常规补货：** 事务性、简洁、以采购订单参考为导向。"根据我们约定的计划，采购订单 #XXXX 的交货周为 MM/DD。"
- **Vendor lead time escalation:** Firm, fact-based, quantifies business impact. "Our analysis shows your lead time has increased from 14 to 22 days over the past 8 weeks. This has resulted in X stockout events. We need a corrective plan by [date]."
  - **供应商交期升级：** 坚定、基于事实、量化业务影响。"我们的分析显示，在过去 8 周内你的交期已从 14 天增加到 22 天。这已导致 X 次缺货事件。我们需要在 [日期] 前提供纠正计划。"
- **Internal stockout alert:** Urgent, actionable, includes estimated revenue at risk. Lead with the customer impact, not the inventory metric. "SKU X will stock out at 12 locations by Thursday. Estimated lost sales: $XX,000. Recommended action: [expedite/reallocate/substitute]."
  - **内部缺货警报：** 紧急、可操作、包含预计风险收入。从客户影响而非库存指标切入。"SKU X 将在周四前在 12 个地点缺货。预计损失销售额：$XX,000。建议行动：[加急/重新分配/替代]。"
- **Markdown recommendation to merchandising:** Data-driven, includes margin impact analysis. Never frame it as "we bought too much" — frame as "sell-through pace requires price action to meet margin targets."
  - **向商品团队提出降价建议：** 数据驱动，包含利润影响分析。永远不要将其框定为"我们买太多了"——而是"销售穿透速度需要价格行动来达到利润目标。"
- **Promotional forecast submission:** Structured, with baseline, lift, and post-promo dip called out separately. Include assumptions and confidence range. "Baseline: 500 units/week. Promotional lift estimate: 180% (900 incremental). Post-promo dip: −35% for 2 weeks. Confidence: ±25%."
  - **促销预测提交：** 结构化，分别标注基线、提升和促销后下降。包含假设和置信区间。"基线：500 件/周。促销提升估算：180%（增量 900）。促销后下降：-35%，持续 2 周。置信度：±25%。"
- **New product forecast assumptions:** Document every assumption explicitly so it can be audited at post-mortem. "Based on analogs [list], we project 200 units/week in weeks 1–4, declining to 120 units/week by week 8. Assumptions: price point $X, distribution to 80 doors, no competitive launch in window."
  - **新产品预测假设：** 明确记录每个假设，以便在复盘时审计。"基于类比品 [列表]，我们预测第 1-4 周为 200 件/周，到第 8 周降至 120 件/周。假设：价格 $X，铺货 80 家门店，窗口期内无竞争产品发布。"

Brief templates appear above. Adapt them to your supplier, sales, and operations planning workflows before using them in production.
上面展示了简要模板。在生产使用前，根据你的供应商、销售和运营规划工作流程进行调整。

## Escalation Protocols
## 升级协议

### Automatic Escalation Triggers
### 自动升级触发器

| Trigger | Action | Timeline |
| 触发器 | 行动 | 时间线 |
|---|---|---|
| Projected stockout on A-item within 7 days | Alert demand planning manager + category merchant | Within 4 hours |
| A 类商品 7 天内预计缺货 | 警报需求规划经理 + 品类买手 | 4 小时内 |
| Vendor confirms lead time increase > 25% | Notify supply chain director; recalculate all open POs | Within 1 business day |
| 供应商确认交期增加 > 25% | 通知供应链总监；重新计算所有未结采购订单 | 1 个工作日内 |
| Promotional forecast miss > 40% (over or under) | Post-promo debrief with merchandising and vendor | Within 1 week of promo end |
| 促销预测偏差 > 40%（高或低） | 与商品团队和供应商进行促销后复盘 | 促销结束后 1 周内 |
| Excess inventory > 26 weeks of supply on any A/B item | Markdown recommendation to merchandising VP | Within 1 week of detection |
| 任何 A/B 类商品过量库存 > 26 周供应 | 向商品副总裁提出降价建议 | 检测到后 1 周内 |
| Forecast bias exceeds ±10% for 4 consecutive weeks | Model review and re-parameterization | Within 2 weeks |
| 预测偏差连续 4 周超过 ±10% | 模型审查和重新参数化 | 2 周内 |
| New product sell-through < 40% of plan after 4 weeks | Assortment review with merchandising | Within 1 week |
| 新产品 4 周后销售穿透 < 计划的 40% | 与商品团队进行品类审查 | 1 周内 |
| Service level drops below 90% for any category | Root cause analysis and corrective plan | Within 48 hours |
| 任何品类服务水平降至 90% 以下 | 根本原因分析和纠正计划 | 48 小时内 |

### Escalation Chain
### 升级链条

Level 1 (Demand Planner) → Level 2 (Planning Manager, 24 hours) → Level 3 (Director of Supply Chain Planning, 48 hours) → Level 4 (VP Supply Chain, 72+ hours or any A-item stockout at enterprise customer)
第 1 级（需求计划员）→ 第 2 级（规划经理，24 小时）→ 第 3 级（供应链规划总监，48 小时）→ 第 4 级（供应链副总裁，72+ 小时或任何企业客户 A 类商品缺货）

## Performance Indicators
## 绩效指标

Track weekly and trend monthly:
每周跟踪，月度趋势：

| Metric | Target | Red Flag |
| 指标 | 目标 | 红色警报 |
|---|---|---|
| WMAPE (weighted mean absolute percentage error) | < 25% | > 35% |
| WMAPE（加权平均绝对百分比误差） | < 25% | > 35% |
| Forecast bias | ±5% | > ±10% for 4+ weeks |
| 预测偏差 | ±5% | 连续 4+ 周 > ±10% |
| In-stock rate (A-items) | > 97% | < 94% |
| 在库率（A 类商品） | > 97% | < 94% |
| In-stock rate (all items) | > 95% | < 92% |
| 在库率（所有商品） | > 95% | < 92% |
| Weeks of supply (aggregate) | 4–8 weeks | > 12 or < 3 |
| 周供应量（合计） | 4-8 周 | > 12 或 < 3 |
| Excess inventory (>26 weeks supply) | < 5% of SKUs | > 10% of SKUs |
| 过量库存（>26 周供应） | < 5% SKU | > 10% SKU |
| Dead stock (zero sales, 13+ weeks) | < 2% of SKUs | > 5% of SKUs |
| 死库存（零销售，13+ 周） | < 2% SKU | > 5% SKU |
| Purchase order fill rate from vendors | > 95% | < 90% |
| 供应商采购订单完成率 | > 95% | < 90% |
| Promotional forecast accuracy (WMAPE) | < 35% | > 50% |
| 促销预测准确性（WMAPE） | < 35% | > 50% |

## Additional Resources
## 其他资源

- Pair this skill with your SKU segmentation model, service-level policy, and planner override audit log.
  - 将此技能与你的 SKU 细分模型、服务水平策略和计划员覆盖审计日志配对使用。
- Store post-mortems for promotion misses, vendor delays, and forecast overrides next to the planning workflow so the edge cases stay actionable.
  - 将促销失误、供应商延迟和预测覆盖的复盘存储在规划工作流程旁边，使边缘案例保持可操作性。
