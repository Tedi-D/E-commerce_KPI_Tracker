Conversion Rate:
 WITH totalpurchases AS ( 
  SELECT 
    COUNT(DISTINCT CASE WHEN event_name = 'purchase' THEN session_id END) AS total_purchases 
  FROM `prism-insights.warehouse_PT.funnelevents` 
), 
totalsessions AS ( 
  SELECT 
    COUNT(DISTINCT session_id) AS total_sessions 
  FROM `prism-insights.warehouse_PT.sessions` 
) 
SELECT 
  total_purchases, 
  total_sessions, 
  ROUND(total_purchases / total_sessions, 4) AS conversion_rate 
FROM totalpurchases, totalsessions; 
 

Profit Margin by Cat and Revenue by Date:
SELECT 
DATE_TRUNC(t.date,MONTH) AS MonthStart, 
ROUND(SUM(transaction_revenue),2) AS TotalRevenue, 
FROM `prism-insights.warehouse_PT.transactions` AS t 
JOIN `prism-insights.warehouse_PT.transactionsanditems` AS ti 
ON t.transaction_id = ti.transaction_id 
JOIN `prism-insights.warehouse_PT.productattributes` AS pa 
ON ti.item_id = pa.item_id 
JOIN `prism-insights.warehouse_PT.product_costs` AS pc 
ON pc.item_id = ti.item_id 
GROUP BY ti.item_id, pa.item_main_category,item_price,MonthStart 
 
SELECT 
   i.item_main_category AS category, 
   ROUND(SUM(t.transaction_revenue), 2) AS total_revenue, 
   ROUND(SUM(pc.cost_of_item * ti.item_quantity), 2) AS total_cost, 
   ROUND(SUM(t.transaction_revenue) - SUM(pc.cost_of_item * ti.item_quantity), 2) AS profit 
FROM prism-acquire.prism_acquire.transactions t 
JOIN prism-acquire.prism_acquire.transactionsanditems ti ON t.transaction_id = ti.transaction_id 
JOIN prism-acquire.prism_acquire.productattributes i ON ti.item_id = i.item_id 
JOIN prism-acquire.prism_acquire.product_costs pc ON ti.item_id = pc.item_id 
GROUP BY i.item_main_category 
ORDER BY profit DESC; 
 
 

Ret Rate Over Time:
WITH new_users AS ( 
    SELECT 
      u.user_crm_id, 
      MIN(t.date) AS first_purchase 
    FROM `warehouse_PT.users` u 
    JOIN `warehouse_PT.transactions` t 
      ON u.user_crm_id = t.user_crm_id 
    GROUP BY u.user_crm_id 
    HAVING first_purchase BETWEEN '2020-01-01' AND '2021-12-31' 
),cohorted_users AS ( 
    SELECT 
      user_crm_id, 
      FORMAT_DATE('%Y-%m', first_purchase) AS cohort_month 
    FROM new_users 
),user_activity AS ( 
    SELECT 
      t.user_crm_id, 
      FORMAT_DATE('%Y-%m', t.date) AS activity_month 
    FROM `warehouse_PT.transactions` t 
    WHERE t.date >= '2020-01-01' AND t.date <= '2022-12-31' 
),retention AS ( 
    SELECT 
      cu.cohort_month, 
      ua.activity_month, 
      COUNT(DISTINCT ua.user_crm_id) AS active_users 
    FROM cohorted_users cu 
    JOIN user_activity ua 
      ON cu.user_crm_id = ua.user_crm_id 
    GROUP BY cu.cohort_month, ua.activity_month 
),cohort_sizes AS ( 
    SELECT 
      cohort_month, 
      COUNT(*) AS cohort_size 
    FROM cohorted_users 
    GROUP BY cohort_month 
)SELECT 
  r.cohort_month, 
  r.activity_month, 
  r.active_users, 
  c.cohort_size, 
  ROUND(r.active_users * 100.0 / c.cohort_size, 2) AS retention_rate_percentage 
FROM retention r 
JOIN cohort_sizes c 
  ON r.cohort_month = c.cohort_month 
ORDER BY r.cohort_month, r.activity_month; 
 

REVENUE, PROFIT, COST, Profit Margin:
SELECT 
   SUM(t.transaction_total) AS total_revenue, 
   SUM(pc.cost_of_item * ti.item_quantity) AS total_cost, 
   SUM(t.transaction_total) - SUM(pc.cost_of_item * ti.item_quantity) AS profit, 
   ROUND( 
 	(SUM(t.transaction_total) - SUM(pc.cost_of_item * ti.item_quantity)) / SUM(t.transaction_total) * 100, 
 	2 
   ) AS profit_margin_percentage 
FROM prism-acquire.prism_acquire.transactions t 
JOIN prism-acquire.prism_acquire.transactionsanditems ti 
  ON t.transaction_id = ti.transaction_id 
JOIN prism-acquire.prism_acquire.productattributes i 
  ON ti.item_id = i.item_id 
JOIN prism-acquire.prism_acquire.product_costs pc 
  ON ti.item_id = pc.item_id; 
 
SELECT 
   i.item_main_category AS category, 
   SUM(t.transaction_total) AS total_revenue, 
   SUM(pc.cost_of_item * ti.item_quantity) AS total_cost, 
   SUM(t.transaction_revenue) - SUM(pc.cost_of_item * ti.item_quantity) AS profit 
FROM prism-acquire.prism_acquire.transactions t 
JOIN prism-acquire.prism_acquire.transactionsanditems ti ON t.transaction_id = ti.transaction_id 
JOIN prism-acquire.prism_acquire.productattributes i ON ti.item_id = i.item_id 
JOIN prism-acquire.prism_acquire.product_costs pc ON ti.item_id = pc.item_id 
GROUP BY i.item_main_category 
ORDER BY profit; 
 
CPA:
SELECT 
  traffic_source, 
  COUNT(DISTINCT session_id) / 69874 AS relative_cpa 
FROM `prism-acquire.prism_acquire.sessions` 
WHERE date BETWEEN '2020-01-01' AND '2022-12-31' 
GROUP BY traffic_source; 
 
 
 
CVR – funnel events:
SELECT 
   event_name, 
   COUNT(DISTINCT item_id) AS event_count 
FROM prism-acquire.prism_acquire.funnelevents 
GROUP BY event_name 
ORDER BY event_count DESC; 
 
REVENUE AND PROFIT MARGIN:
SELECT 
   i.item_main_category AS category, 
   ROUND(SUM(t.transaction_revenue), 2) AS total_revenue, 
   ROUND(SUM(pc.cost_of_item * ti.item_quantity), 2) AS total_cost, 
   ROUND(SUM(t.transaction_revenue) - SUM(pc.cost_of_item * ti.item_quantity), 2) AS profit 
FROM prism-acquire.prism_acquire.transactions t 
JOIN prism-acquire.prism_acquire.transactionsanditems ti ON t.transaction_id = ti.transaction_id 
JOIN prism-acquire.prism_acquire.productattributes i ON ti.item_id = i.item_id 
JOIN prism-acquire.prism_acquire.product_costs pc ON ti.item_id = pc.item_id 
GROUP BY i.item_main_category 
ORDER BY profit DESC; 
 
