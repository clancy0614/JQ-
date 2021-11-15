
# 第一题

spread 变宽的次数是 233，其中两侧都向外移动的情况占比 0.429%，单侧占比99.571%。spread 变窄的次数是 235

market size 随时间的分布，横坐标为时间，纵坐标为market size

![image](https://user-images.githubusercontent.com/88695029/141720619-3a1510af-d45b-4fab-a5c3-7a1d829d22ad.png)

# 第二题

market size 的柱状分布图，横坐标为 market size，纵坐标为对应的 market size 出现的次数。

可以发现，在出现 mid price 有变化但 spread 不变的情况下，market size 集中分布在 500 附近，相对来说属于 market size 比较小的范围（从图1中我们可以发现 market size 最高可达 4000 以上）。

这种情况的原因可能是较大的 market size 通常反应了买卖双方活跃度较高，这种情况下 spread 可能会产生较大波动而并非保持不变。

![image](https://user-images.githubusercontent.com/88695029/141720855-cc974d9f-a37a-4a06-badb-880e93ebac0f.png)

# 第三题

假设从i时刻到i+1时刻，volume 增加量为 dv。

### 情况1

如果i+1时刻读取的 lastprice 比i时刻的 midprice 要高（低），说明市场方向是向买（卖）移动，这种情况下i时刻的最低价的 ask 订单被买方主动匹配（最高价的 bid 买单被卖方主动匹配）。

* 有可能i时刻的最低价 ask 订单会全部成交（最高价 bid 订单全部成交）。举个例子，给定数据中从 updatecount = 3 到 4


   | updateCount |   lastprice |  volume | bidsize | bid  |   ask |   asksize |
   | ------------- | -----------| -------- | ------| ------| ------| --------|
   | 3  |     26.77  |     361   |  518  |   26.77 |   26.78  |  1 |           
   | 4  |     26.79   |    374   |  42   |   26.78 |  26.79  | 44  | 

  市场的最新成交价是26.79，比前一时刻的 midprice 更高，所以我们判断市场在向买移动。在 updatecount = 3的一行中，26.78元的 ask 订单被买方主动成交，对应的asksize是1。但是dv是13，剩下的12可以分配到当前的最新成交价，即26.79

* 也有可能所有 dv 都能被 asksize（或bidsize）的变化量所解释。

### 情况2

如果i+1时刻读取的 lastprice 等于i时刻的 midprice，可以把dv全部分配到当前 lastprice 的价位。

### 结论

通过这种方法，我们可以将 dv 分配在至多两档价格里，一档是vol_lastprice，另一档是 vol_lastbid (或vol_lastask)

注： 由于时间太紧，并没有写出代码计算 vol_lastprice, vol_lastbid, vol_lastask


# 第四题

在第三题中，我们提出了一个估算 vol_lastprice，vol_lastbid, vol_lastask 的方法。通过这个方法我们能计算出 bid price (或者 ask price) 对应的成交量，再把这个成交量和 bid size (或者ask size) 的变化量相加，即可得到 sizeDeltaAtBid 和 sizeDeltaAtAsk.

```
df['sizeDeltaAtBid'] = df['vol_lastbid'] + df['bidsize'].diff()
df['sizeDeltaAtAsk'] = df['vol_lastask'] + df['asksize'].diff()
```

# 第五题

首先找出所有 aggressive side，如果 midprice 往高价移动，则 top order 是第一档的 bid；反之则是第一档的 ask。所有 top order 中，是 bid 有 565 次，是 ask 有 537 次。

假如某一个 bid 是当时的 top order，对应的 bid size 是1000。在第三题中我们给出了一个计算有多少成交量对应的价格是在 last bid 的方法, 用这个我们能计算 top order 中成交的数量，比如说有100个。剩下的900个没有被即时成交，在之后有可能会被更高价的 bid 覆盖，即变成非BBO。


# 第六题

通过 pandas 的 shift(-40) 指令我们可以实现 （当前时刻的 excution price） - （40单位时间后的 mid price）的操作从而计算定义的 return
