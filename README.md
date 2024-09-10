# Module 4 Challenge

## Wholesale Analysis

This notebook analyses the wholesale data.

### Requirements

#### Explore the Data

* View the column names.

``` python
df.columns
```

* Use the describe function.

``` python
df.describe(include=numpy.number)
```

* Correctly identify the category with the most entries.

``` python
df.value_counts("category").head(3)
```

* For the category with the most entries, correctly identify the subcategory with the most entries.

``` python
df[df["category"] == df.value_counts("category").index[0]].value_counts("subcategory").head(1)
```

* Correctly identify the 5 clients with the most entries in the data.

``` python
df.value_counts("client_id").head(5)
```

* Store the client ids of those top 5 clients in a list.

``` python
topfive = df.value_counts("client_id").head(5).index.tolist()
```

* Display the total units (the qty column) that the client with the most entries ordered.

``` python
print(f"The client with client ID {topfive[0]} ordered " + str(df[df["client_id"] == topfive[0]]["qty"].sum()) + " units")
```

#### Transform the Data

* Create a column that calculates the subtotal for each line using the unit_price and the qty.

``` python
df["subtotal"] = df["unit_price"]*df["qty"]
```

* Create a column for shipping price. Assume a shipping price of $7 per pound for orders over 50 pounds and $10 per pound for items 50 pounds or under.

``` python
df["shipping_price"] = (df["unit_weight"]*df["qty"]).apply(lambda x: x*7 if x>50 else x*10)
```

* Create a column for the total price using the subtotal and the shipping price along with a sales tax of 9.25%.

``` python
df["total_price"] = ((df["subtotal"] + df["shipping_price"]) * (1 + 9.25 / 100)).round(2)
```

* Create a column for the cost of each line using unit cost, qty, and shipping price (assume the shipping cost is exactly what is charged to the client).

``` python
df["cost"] = df["unit_cost"] * df["qty"] + df["shipping_price"]
```

* Create a column for the profit of each line using line cost and line price.

``` python
df["profit"] = df["total_price"] - df["cost"]
```

#### Confirm Your Work

* Confirm that Order ID 2742071 had a total price of $152,811.89.

``` python
print("Order ID 2742071 had a total price of $" + str(df[df["order_id"] == 2742071]["total_price"].sum().round(2)))
```

* Confirm that Order ID 2173913 had a total price of $162,388.71.

``` python
print("Order ID 2173913 had a total price of $" + str(df[df["order_id"] == 2173913]["total_price"].sum().round(2)))
```

* Confirm that Order ID 6128929 had a total price of $923,441.25.

``` python
print("Order ID 6128929 had a total price of $" + str(df[df["order_id"] == 6128929]["total_price"].sum().round(2)))
```

#### Summarize and Analyze

* Calculate the total revenue from each of the top 5 clients in Part 1.

``` python
[print(f"Client {topfive[k]} spent $" + str(df[df["client_id"]==topfive[k]]["total_price"].sum().round(2))) for k in range(0,5)]
```

* Create a summary DataFrame showing the totals for the top 5 clients with the following information: total units purchased, total shipping price, total revenue, and total profit.

``` python
def m(x):
    return {"cid":x,
            "tu":df[df["client_id"]==x]["qty"].sum(),
            "ts":df[df["client_id"]==x]["shipping_price"].sum().round(2),
            "tr":df[df["client_id"]==x]["total_price"].sum().round(2),
            "tp":df[df["client_id"]==x]["profit"].sum().round(2)
    }

d = []

[d.append(m(k)) for k in topfive]

d = pd.DataFrame(d)

d
```

* Create a function to change the currency to millions of dollars. Format the data and rename the columns to names suitable for presentation. Then, sort the DataFrame in descending order by total profits.

``` python
def currency_format_millions(x):
    return x/(10**6)
```

* Write a brief 2-3 sentence summary of your findings.

``` python
"""
The findings are that client 24741 is the biggest consumer by revenue of our products with over ten times the profit of any other client.
Even though client 33615 had the most entries in the dataset, client 24741 has the most revenue.
"""
```
