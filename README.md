# supply-chain-delivery-performance-power-bi
Why Are Orders Late? A Supply Chain Delivery Performance Analysis (Power BI)

Power BI dashboard analysing late deliveries in the DataCo supply chain dataset: DAX measures, a multi-page interactive report and three recommendations.

**Get the dataset here:** www.kaggle.com/datasets/kazzyagu/supply-chain-delivery-performance-power-bi

---

# Supply Chain Delivery Performance Analysis (Power BI)

An end-to-end analysis of delivery performance using the DataCo Smart Supply Chain dataset. The cleaned data is loaded into Power BI, where DAX measures answer the business questions, an interactive report presents the findings, and three recommendations are given for management.

This project follows an earlier Excel version of the same analysis (pivot tables and dashboard): [supply-chain-delivery-performance-excel](https://github.com/kazzyagu/supply-chain-delivery-performance-excel).

## Business questions

1. How many orders are delivered late?
2. What percentage of orders are late?
3. Which shipping methods, markets and product categories have the most late deliveries?
4. What should management do to improve supply chain performance?

## Dataset

- **Source:** check link above
- **Size:** about 180,000 order-item rows, about 65,700 unique orders
- **Note:** the raw file is not included in this repository. Download it from the source above.

## Tools and skills used

- **Power BI Desktop:** data model, report pages, slicers, bookmarks and page navigation
- **DAX:** `CALCULATE`, `DISTINCTCOUNT`, `DIVIDE`, `AVERAGE`, variables
- **Visuals:** KPI cards, clustered bar charts, column charts, donut chart, Top N filtering
- **Excel:** data cleaning (the `Clean_Data` sheet is the source for the report)
- **Dashboard design:** custom dark theme, consistent page layout, page navigation buttons

## Method

1. **Prepared the data.** Used the cleaned `Clean_Data` sheet from the Excel project: duplicates and blanks checked, Spanish country names translated into English, and columns with no information reviewed.
2. **Defined the metrics.** Counted unique orders (`DISTINCTCOUNT` of Order Id) instead of rows, because one order can contain several products.
3. **Defined "delivered".** Used Order Status = COMPLETE + CLOSED and excluded canceled orders.
4. **Created a Delay Days column** (real shipping days minus scheduled shipping days) to measure how late orders were.
5. **Wrote DAX measures** for total orders, late orders, late rate and average delay (see below).
6. **Built the report pages** and wrote the recommendations.

## Key DAX measures

```DAX
Total Orders =
CALCULATE(
    DISTINCTCOUNT(Clean_Data[Order Id]),
    Clean_Data[Order Status] IN {"COMPLETE", "CLOSED"}
)
```

```DAX
Late Rate % =
VAR LateOrders =
    CALCULATE(
        DISTINCTCOUNT(Clean_Data[Order Id]),
        Clean_Data[Delivery Status] = "Late delivery"
    )
VAR AllOrders =
    CALCULATE(
        DISTINCTCOUNT(Clean_Data[Order Id]),
        Clean_Data[Delivery Status] <> "Shipping canceled"
    )
RETURN
    DIVIDE(LateOrders, AllOrders)
```

```DAX
Avg Days Promised = AVERAGE(Clean_Data[Days for shipment (scheduled)])
Avg Days Actual   = AVERAGE(Clean_Data[Days for shipping (real)])
```

## Report pages

| Page | Question it answers | What is on it |
|---|---|---|
| **Delivery Performance** | How bad is the late-delivery problem overall? | KPI cards (total orders, late orders, late rate, average days late, on-time orders), delivery timing donut, late orders by market |
| **Shipping Modes/why orders are late** | Which shipping methods fail, and why? | Late rate and late orders by shipping mode, promised vs actual days by shipping mode, order counts per mode |
| **Recommendations** | What should management do? | Three recommendations with supporting evidence |

## Key findings

*Basis: Order Status = COMPLETE + CLOSED, excluding canceled orders.*

| Measure | Result |
|---|---|
| Orders | 28,965 |
| Late orders | 16,633 |
| Late rate | 57.3% |
| Average delay of late orders | 1.6 days |

### Late rate by shipping mode

| Shipping method | Scheduled days | Real days (avg) | Late rate |
|---|---|---|---|
| First Class | 1 | 2.0 | 100% |
| Second Class | 2 | 4.0 | 80.1% |
| Same Day | 0 | 0.5 | 48.5% |
| Standard Class | 4 | 4.0 | 39.8% |

- Premium shipping methods are promised faster than they are delivered, so they are late by design.
- The late rate stays between about 56% and 59% across markets, months, weekdays and quarters, so the issue is company-wide rather than regional or seasonal.
- Most late orders miss their scheduled date by only 1 to 2 days.
- Cleats, Men's Footwear and Women's Apparel have the most late orders. These are counts, so they partly reflect order volume.

## Recommendations

1. **Reset the promised delivery times for First Class and Second Class** so they match real performance, or improve fulfillment until the promise is met.
2. **Treat lateness as a company-wide process issue.** Review dispatch and carrier handoff steps and set a target late rate.
3. **Fix how delivery status is recorded and track it monthly.** Late delivery is recorded on orders that are still pending payment or processing, which makes the status unreliable.

## Dashboard

![Delivery Performance](https://github.com/kazzyagu/supply-chain-delivery-performance-power-bi/blob/main/delivery%20performance.PNG)

![Why Orders are Late](https://github.com/kazzyagu/supply-chain-delivery-performance-power-bi/blob/main/Wht%20orders%20are%20late.PNG)

![Recommendations](https://github.com/kazzyagu/supply-chain-delivery-performance-power-bi/blob/main/recommendations.PNG)

## Repository contents

- `supply-chain-delivery-performance.pbix` - the Power BI report (link to download above)
- `images/` - screenshots of the report pages
- `README.md` - this file

## Limitations

- "Delivered" is not defined in the dataset, so I used COMPLETE + CLOSED. This is an assumption. The late rate is similar under either definition (about 58% vs 57%).
- Product category counts overlap, because an order with items in several categories appears under each one.

## Author

Kingsley Agu - [LinkedIn](https://www.linkedin.com/in/coachkcagu/) - kazzyagu at gmail com
