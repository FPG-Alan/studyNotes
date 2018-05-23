# 验证
Marketing API 验证发生在多种对象, 或者在不同对象之间.

- buying_type, billing_event and bid_strategy
- 目标和 optimization_goal
- 优化目标和账单事件
- 目标和Creatives
- Ad Creative 验证
- 目标和 tracking 规格
- 目标和推广对象
- 目标和广告占位
- 目标,优化目标 和 attribution_spec

## buying_type, billing_event and bid_strategy
buying_type 是广告主付费的方式, 在 campaign 等级定义, 大部分时间我们使用 `AUCTION`, 但有一些特殊情况我们要么根据预测结果进行结算, 也就是 `RESERVED`, 或者使用固定价格的方式来协商广告商将支付的价格, 即 `FIXED_CPM`.

设置了 buying_type 的 Campaign 要求其下的 AdSet 设置其 billing_event

对应不同的 buying_type 有效的 billing_event 如下表所示: 

 |                 | AUCTION | RESERVED | FIXED_CPM |
 | --------------- | ------- | -------- | --------- |
 | IMPRESSIONS     | ✓       | ✓        | ✓         |
 | LINK_CLICKS     | ✓       |          |           |
 | APP_INSTALLS    | ✓       |          |           |
 | PAGE_LIKES      | ✓       |          |           |
 | POST_ENGAGEMENT | ✓       |          |           |
 | VIDEO_VIEWS     | ✓       |          |           |

## bid_strategy on Ad Sets
3.0版本中, 我们在 AdSet 上引入了 bid_strategy 来替代两个布尔字段 is_autobid and is_average_price_pacing, bid_strategy 字段使你可以直接选择一种最适合你的特定商业目标的出价策略.

Facebook 支持三种出价策略: `LOWEST_COST_WITHOUT_CAP`, `LOWEST_COST_WITH_BID_CAP` 和 `TARGET_COST`, 要详细了解每个出价策略的权衡, 参见[Ads Help Center, Bid Strategies](https://www.facebook.com/business/help/1619591734742116)

下面是当你在 adset 上使用了 bid_strategy 和 bid_amount 的额外验证:

| Bid Strategy | Bid Requirements |
| ------------ | ---------------- |
| LOWEST_COST_WITHOUT_CAP | 不能指定 bid_amount |
| LOWEST_COST_WITH_BID_CAP | 当更新或创建一个使用此策略的Ad 对象时, 必须指定 bid_amount |
| TARGET_COST | 当更新或创建一个使用此策略的Ad 对象时, 必须提供 bid_amount 作为目标成本.  |

更多信息参见 [Ads Buying and Optimization, Bid Strategy](https://developers.facebook.com/docs/marketing-api/bidding-and-optimization#auto)


