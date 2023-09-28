# Attribution Model

SQL and markdown.

## Orders by Channel

```sql orders
select 
    channel,
    date_trunc('month', order_datetime) as month,
    count(*) as orders
from orders
group by all
order by month desc, orders desc
```

The smallest channels are currently <Value data={orders} row=5/>, <Value data={orders} row=4/> and <Value data={orders} row=3/>

<AreaChart
    title='Orders attributed to each channel'
    data={orders}
    x=month
    y=orders
    series=channel
/>

<DataTable data={orders}/>

