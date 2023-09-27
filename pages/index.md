# Attribution Model

## Orders by Channel

```sql orders
select 
    channel,
    date_trunc('month', order_datetime) as month,
    count(*) as orders
from orders
where order_datetime >= '2021-01-01'
group by all
order by month desc, orders
```

The largest channels are currently <Value data={orders} row=5/>, <Value data={orders} row=4/> and <Value data={orders} row=3/>

<BarChart
    title='Orders attributed to each channel'
    data={orders}
    x=month
    y=orders
    series=channel
/>

