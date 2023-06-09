# Instacart Data Dictionary

This is a data dictionary for the Instacart dataset.

## Files

### `aisles.csv`

Each row corresponds to an aisle.

- `aisle_id`: ID number that uniquely identifies each grocery aisle category
    - As a Primary Key, let's change this to a **string**
- `name`: name of the aisle
    - rename from `aisle` to `name`
    - Keep this a **string**

### `departments.csv`

Each row corresponds to a department.

- `department_id`: ID number that uniquely identifies each grocery department category
    - As a Primary Key, let's change this to a **string**
- `name`: name of the department
    - rename from `department` to `name`
    - Keep this a **string**

### `instacart_orders.csv`

Each row corresponds to one order on the Instacart app.

- `order_id`: ID number that uniquely identifies each order
    - 0.0031% of these values are duplicate, maybe a mistake?
        - After dropping duplicate rows, all columns are unique
    - As a Primary Key, let's change this to a **string**
- `user_id`: ID number that uniquely identifies each customer account
    - As a Foreign Key, let's change this to a **string**
- `user_order_number`: the number of times this customer has placed an order
    - rename from `order_number` to `user_order_number`
    - Since the values for this are within 0 to 255, let's change this to an **uint8**
- `day_of_week_numeral`: day of the week that the order placed (which day is 0 is uncertain)
    - rename from `order_dow` to `day_of_week_numeral`
    - It's not specified which day of the week 0 starts at, but traditionally 0 = Sunday. We'll make a new columns with this assumption containing a string of the day's name. It should be easy to change this if more information is obtained to show 0 != Sunday.
    - Since the values for this are within 0 to 255, let's change this to an **uint8**
- `order_hour_of_day`: hour of the day that the order was placed
    - Since the values for this are within 0 to 255, let's change this to an **uint8**
- `days_since_prior_order`: number of days since this customer placed their previous order
    - Replace all the NaN values with -1, presumably because the customer in question has not ordered since that order
    - Change to **int8**
- `day_of_week`: Name of the day of the week, assuming `order_dow` 0 = Sunday and the following numbers are consecutive days of the week
    - New column, as **String**

### `order_products.csv`

Each row corresponds to one item placed in an order.

- `order_id`: ID number that uniquely identifies each order
    - As a Composite Key, let's change this to a **string**
- `product_id`: ID number that uniquely identifies each product
    - As a Composite Key, let's change this to a **string**
- `cart_item_id`: the sequential order in which each item was placed in the cart
    - rename from `add_to_cart_order` to `cart_item_id`
    - A Composite Key since a user can order more than one of the same item, but there's no variable for `quantity`, so the sequence it's placed in the cart will have to indicate purchasing multiple
    - There are some missing, but it's only 0.0186% of the entries, so I'll just drop them
    - Since the values for this are within 0 to 255, let's change this to an **uint8**
- `reordered`: 0 if the customer has never ordered this product before, 1 if they have
    - Since the values for this are within 0 to 255, let's change this to an **uint8**

### `products.csv`

Each row corresponds to a unique product that customers can buy.

- `product_id`: ID number that uniquely identifies each product
    - this one's already a string but it should have defaulted to a numeric so there's probably something wrong with the data
        - Tried coerseing `product_id` to be a numberic, but that didn't help much. 
        - Look at dataset: someone put a bunch of `"` at seemingly random intervals. Siiiigh
        - Also there are `\"` occasionally which indicates I can't just delete all the `"`s from the data.
            - I replace all `\"` with some other string that I made sure is not in the data set, `real quote`.
            - I delete all `"`s
            - replace all `real quote`s with `\"`
        - Check again... There's a problem with just line 405, and extra `;`
            - remove and try again... there's more
            - I search with the regex `^(?:[^;]*;){4,}` to see how often this is happening
                - This happens nine more times, not too many, so I manually fix them all.
        - Check again. That's better
    - Now that there's one unique value for each row, it's now a Primary Key, let's change this to a **string**
- `name`: name of the product
    - rename from `product_name` to `name`
    - Looks like a few of these are missing, fill in those names with **__unknown__**
    - Also, convert this to lowercase before checking for duplicates
- `aisle_id`: ID number that uniquely identifies each grocery aisle category
    - As a Foriegn Key, let's change this to a **string**
- `department_id`: ID number that uniquely identifies each grocery department category
    - As a Foriegn Key, let's change this to a **string**