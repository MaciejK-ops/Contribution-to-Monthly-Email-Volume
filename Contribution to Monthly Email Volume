WITH account_monthly_stats AS (
  SELECT
    a.id AS id_account,
    -- Truncate the calculated send date (session date + sent_date offset) to the first day of the month
    DATE_TRUNC(DATE_ADD(s.date, INTERVAL e.sent_date DAY), MONTH) AS month_of_sending,
    -- Count total emails sent by this specific account in the given month
    COUNT(e.id_message) AS sent_month,
    -- Track the latest and earliest email send dates for the account within the month
    MAX(DATE_ADD(s.date, INTERVAL e.sent_date DAY)) AS last_sent_date,
    MIN(DATE_ADD(s.date, INTERVAL e.sent_date DAY)) AS first_sent_date
  FROM `data-analytics-mate.DA.email_sent` AS e
  JOIN `data-analytics-mate.DA.account` AS a
    ON e.id_account = a.id
  JOIN `data-analytics-mate.DA.account_session` AS acs
    ON acs.account_id = a.id
  JOIN `data-analytics-mate.DA.session` AS s
    ON acs.ga_session_id = s.ga_session_id
  GROUP BY 1, 2
)

SELECT
  id_account,
  month_of_sending,
  first_sent_date,
  last_sent_date,
  -- Calculate the percentage share of total emails sent by this account relative to all emails sent in that month
  SAFE_DIVIDE(
    CAST(sent_month AS FLOAT64),
    SUM(sent_month) OVER (PARTITION BY month_of_sending)
  ) * 100 AS sent_msg_percent_from_this_month


FROM account_monthly_stats;
