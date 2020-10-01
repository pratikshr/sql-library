---
title: Salesforce - Active sales rep ranked by revenue for the Quarter-To-Date (QTD)
description: This query shows the active sales reps ranked by revenue. Amount is tallied from closed and won opportunities by the sales reps derived from Salesforce data.
requirements: Collect the `User` and `Opportunity` objects with the Panoply Salesforce data source
usage: This query can be displayed in a tabular or pivot form to display the revenue per active sales rep
modifications: The table in the `FROM` might need to be changed based on Schema and Destination settings in the data source. The Date Range Filter using the `closedate` in the `WHERE` clause can be changed.
---

# Salesforce - Active sales rep ranked by revenue for the Quarter-To-Date (QTD)

```sql
WITH salesforce_user AS (
    SELECT id, email, firstname || ' ' || lastname as rep
    FROM public.salesforce_user
    WHERE salesforce_user.IsActive
)
SELECT
  us.rep as sales_rep,
  us.email,
  sum(op.amount) as revenue
FROM 
    public.salesforce_opportunity op
JOIN 
    salesforce_user us
  ON op.ownerid = us.id
WHERE op.stagename = 'Closed Won'
  AND date_trunc('quarter',closedate) = date_trunc('quarter',current_date)
GROUP BY 1,2
ORDER BY revenue DESC
```

## Query Results Dictionary
Column | Description
---|---
`sales_rep`| Opportunity Owner (Sales Rep)
`email`| Sales Rep Email
`revenue`| Sum of amount from closed/won opportunities by sales rep.