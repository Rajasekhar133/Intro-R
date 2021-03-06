Day 1 Case Study Exercises
================

# Prerequisites

All of the exercises can be solved using the `tidyverse` and
`completejourney` packages. The `completejourney` package is an R data
package that has been created so the full suite of Complete Journey
datasets can be loaded as a library. You can find details about the data
and the variables at <http://bit.ly/completejourney>. In order to use
the data you must first install the package following these steps:

``` r
install.packages('completejourney')
```

Go ahead and load the `tidyverse` and `completejourney` packages:

``` r
library(_______)
library(_______)
```

The exercises that follow will use various data sets included in the
`completejourney` package to
include:

``` r
transactions <- transactions_sample  # just using a sample of the entire data

transactions
products
```

# Data Transformation

The following five exercises are based on concepts covered in the data
transformation (`dplyr`) slides.

-----

## Exercise 1

Create three new variables named `regular_price`, `loyalty_price`, and
`coupon_price` according to the following
logic:

``` r
regular_price = (sales_value + retail_disc + coupon_match_disc) / quantity
loyalty_price = (sales_value + coupon_match_disc) / quantity
coupon_price  = (sales_value - coupon_disc) / quantity
```

This question is designed to strengthen your ability to use the `dplyr`
verb `mutate()` to create new variables from existing ones. It should
also help you develop a better understanding of the discount variables
in `transactions`.

``` r
transactions <- transactions %>% 
  mutate(
    regular_price = _______,
    loyalty_price = _______,
    coupon_price = _______
    )
```

-----

## Exercise 2

Create three new variables named `regular_price`, `loyalty_price`, and
`coupon_price` according to the following
logic:

``` r
regular_price = (sales_value + retail_disc + coupon_match_disc) / quantity
loyalty_price = (sales_value + coupon_match_disc) / quantity
coupon_price  = (sales_value - coupon_disc) / quantity
```

This question is designed to strengthen your ability to use the `dplyr`
verb `mutate()` to create new variables from existing ones. It should
also help you develop a better understanding of the discount variables
in `transactions`.

``` r
transactions <- transactions %>% 
  mutate(
    ______,
    ______,
    ______
    )
```

-----

## Exercise 3

`transactions` includes 20,902 unique product IDs. How many of these
products (not transactions\!) had a regular price of one dollar or less?
What does this count equal when loyalty price is one dollar or less? How
about when coupon price is one dollar or less?

**Hint:** After filtering, select the `product_id` column and count
unique products using the `n_distinct()` function.

This question is designed to strengthen your ability to use the `dplyr`
verbs `filter()` and `select()`.

``` r
# change your input in filter to assess the different options:
#   - regular_price <= 1
#   - loyalty_price <= 1
#   - coupon_price <= 1

# how many products had a regular price of $1 or less
transactions %>% 
  filter(_______) %>% 
  select(product_id) %>% 
  n_distinct()

# how many products had a loyalty price of $1 or less
transactions %>% 
  filter(_______) %>% 
  select(product_id) %>% 
  n_distinct()

# how many products had a coupon price of $1 or less
transactions %>% 
  filter(_______) %>% 
  select(product_id) %>% 
  n_distinct()
```

-----

## Exercise 4

What proportion of baskets are over $10 in sales value?

**Hint:** You need to use `group_by()` and `summarize()`. Depending on
your approach you may or may not use `mutate()`.

This question is designed to strengthen your ability to use the `dplyr`
verbs `group_by()`, and `summarize()`.

``` r
transactions%>%
  group_by(_______) %>%
  summarize(basket_value = _______) %>%
  summarize(proportion_over_10 = _______)
```

-----

## Exercise 5

Which stores had the largest total `sales_value` (hint:
`sum(sales_value, na.rm = TRUE)`? Which stores had the largest average
loyalty discount as defined below?

**Hint:** You can calculate loyalty discount as a percentage of regular
price using the following logic:

``` r
pct_loyalty_disc = 1 - (loyalty_price / regular_price)
```

This question is designed to strengthen your ability to use the `dplyr`
verbs `mutate()`, `group_by()`, `summarize()`, and `arrange()`.

``` r
# find the stores with the largest total `sales_value`
transactions %>%
  group_by(store_id) %>%
  summarize(total_sales_value = _______) %>%
  arrange(_______)
```

``` r
# find the stores with the largest average (mean) loyalty discount
transactions %>%
  mutate(pct_loyalty_disc = _______) %>%
  group_by(_______) %>%
  summarize(avg_pct_loyalty_disc = _______) %>%
  arrange(_______)
```

-----

# Data Visualization

The following five questions are based on concepts covered in the data
visualization (`ggplot2`) slides. They can be answered using the
`transactions` and `products` datasets.

-----

## Exercise 6

Create a histogram of the `quantity` variable in the `transactions`
data. What, if anything, do you find unusual about this visualization?

This question is designed to strengthen your ability to use
`geom_histogram()`.

``` r
ggplot(data = ______, aes(x = ______)) + 
  geom_histogram()
```

-----

## Exercise 7

Use a line graph to plot total sales value by `date` (I have included a
mutate statement that parses the YYY-MM-DD from the
`transaction_timestamp` variable and names it `date`). What kind of
patterns do you find?

This question is designed to strengthen your ability to use `dplyr`
verbs in combination with `geom_line()`.

``` r
transactions %>% 
  mutate(date = lubridate::as_date(transaction_timestamp)) %>%
  group_by(______) %>% 
  summarize(total_sales_value = ______) %>%
  ggplot(aes(x = ______, y = ______)) + 
  geom_line()
```

-----

## Exercise 8

Use a bar graph to compare the total sales values of national and
private-label brands.

**Hint**: Because `transactions` does not contain product metadata, run
the code below to create a new dataset with additional product
information in it. Use `my_transaction_data` for your
answer.

``` r
my_transaction_data <- left_join(transactions, products, by = 'product_id')
```

This question is designed to strengthen your ability to use `dplyr`
verbs in combination with `geom_col()`.

``` r
my_transaction_data %>%
  group_by(brand) %>%
  summarize(total_sales_value = ______) %>%
  ggplot(aes(x = ______, y = ______)) + 
  geom_col()
```

-----

## Exercise 9

Building on the previous exercise, suppose you want to understand
whether the retailer’s customers’ preference for national brands
(compared to private-label brands) is stronger in the soft drink
category than it is in the cheese category. Examine this supposition by
using a stacked bar graph to compare the split between national and
private-label brands for soft drinks and cheeses.

**Hint**: Follow these three steps to create your plot:

  - Filter `my_transaction_data` to include only transactions with
    `product_category` equal to “SOFT DRINKS” or “CHEESE”
  - Calculate total sales value by `product_category` and `brand`
  - Create the bars using `geom_col` and include `fill = brand` within
    `aes()`

<!-- end list -->

``` r
my_transaction_data %>%
  filter(product_category %in% c('SOFT DRINKS', 'CHEESE')) %>%
  group_by(brand, product_category) %>%
  summarize(total_sales_value = sum(sales_value)) %>%
  ggplot(aes(x = product_category, y = total_sales_value, fill = brand)) + 
  geom_col()
```

-----

## Exercise 10

The code below filters `my_transaction_data` to include only peanut
better, jelly, and jam transactions. Then it creates a new variable
named `package_size` equal to product size in ounces. Create a bar graph
with `pb_and_j_data` to visualize the distribution of the retailer’s
PB\&J transactions by product size. Which two product sizes are the most
popular?

``` r
pb_and_j_data <- my_transaction_data %>% 
  filter(product_category == 'PNT BTR/JELLY/JAMS') %>%
  mutate(
    product_size = as.factor(as.integer(gsub('([0-9]+)([[:space:]]*OZ)', '\\1',
                                             package_size)))
  )
```

``` r
ggplot(pb_and_j_data, aes(x = ______)) + 
  ______
```
