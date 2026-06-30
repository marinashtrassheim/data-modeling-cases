## Scenario

You have a `dim_product` table. A product belongs to a subcategory → category → supercategory.  
In a **Star Schema**, this is one wide table. In a **Snowflake Schema**, it is three related tables.

## Task

Describe the pros and cons of both approaches in this case.  
When would you choose Star, and when Snowflake?  
How would your answer change if:  
a) the team consists only of analysts with no SQL experience;  
b) the category hierarchy changes every quarter?
