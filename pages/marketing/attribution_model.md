# Attribution Model


## Orders by Channel

```sql orders_by_channel
select 
    channel,
    date_trunc('month', order_datetime) as month,
    channel_month,
    count(*) as orders
from orders
where order_datetime >= '2021-01-01'
group by all
order by month desc, orders
```

The largest channels are currently <Value data={orders_by_channel} row=5/>, <Value data={orders_by_channel} row=4/> and <Value data={orders_by_channel} row=3/>.

<DataTable data={orders_by_channel}/>



<AreaChart
    title='Orders attributed to each channel'
    data={orders_by_channel}
    x=month
    y=orders
    series=channel
/>

## CPA - 2021


```sql channel_cpa
select 
channel_month,
month as month,
marketing_channel,
sum(spend) as total_spend_usd,
sum(orders) as total_orders,
round(sum(spend) / sum(orders),2) as cpa_usd0

from ${orders_by_channel}
left join marketing_spend using(channel_month)

group by 1,2,3
order by 6
```

```sql total_cpa
select 
sum(total_spend_usd) as tot_spend_usd,
sum(total_orders) as tot_orders,
round(sum(total_spend_usd) / sum(total_orders),2) as blended_cpa_usd,
14 as target_cpa_usd,
(sum(total_spend_usd) / sum(total_orders))/target_cpa_usd - 1 as diff_vs_target_pct
from ${channel_cpa}
```

```sql most_efficient_channels
select 
marketing_channel,
round(total_spend_usd,0) as spend_usd0k,
total_orders,
cpa_usd0

from ${channel_cpa}

where month >= '2021-12-01'
and marketing_channel is not null
```



<BigValue 
    data={total_cpa} 
    value=tot_orders
    fmt='0,000'
    title='Total Orders'
/>

<BigValue 
    data={total_cpa} 
    value=tot_spend_usd
    title='Total Spend'
/>

<BigValue 
    data={total_cpa} 
    value=blended_cpa_usd
    comparison=diff_vs_target_pct
    comparisonTitle='vs target'
    downIsGood
    title='Blended CPA'
/>



{#if ((total_cpa[0].blended_cpa_usd / 14 - 1) < 0) }

CPA is below target, so you may wish to investigate spending more in efficient channels, or testing new channels:

The most efficient channels are currently <Value data={most_efficient_channels}/> (CPA <Value data={most_efficient_channels} column=cpa_usd0/>) and <Value data={most_efficient_channels} row=1/> (CPA <Value data={most_efficient_channels} row=1 column=cpa_usd0/>).


{:else}

CPA is above target - you may wish to reduce marketing spend.

{/if}





<LineChart
    title='Cost per Acquisition by Channel, 2021'
    data={channel_cpa}
    x=month
    y=cpa_usd0
    series=marketing_channel
/>

### N.B. TikTik Ads are being tested as a new channel and are expected to become more efficient over time



<ScatterPlot
    title='CPA vs Spend, Paid channels, Dec 2021'
    subtitle='The best channels are in the bottom right, with high reach and low cost'
    data={most_efficient_channels}
    x=spend_usd0k
    y=cpa_usd0
    xAxisTitle=true
    yAxisTitle=true
    pointSize=20
    series=marketing_channel
    
/>


