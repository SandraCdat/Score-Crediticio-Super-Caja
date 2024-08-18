# CONSULTAS BIG QUERY
## 1. Cuartiles
Crear cuartiles para cada variable
``` 
WITH quartiles AS (
  SELECT user_id, 
  CASE 
    WHEN age <= 41 THEN 1
    WHEN age > 41 AND age <= 52 THEN 2
    WHEN age > 52 AND age <= 63 THEN 3
    WHEN age > 63 THEN 4
  END AS age_quartile,
  CASE 
    WHEN last_month_salary <= 3431 THEN 1
    WHEN last_month_salary > 3431 AND last_month_salary <= 5416 THEN 2
    WHEN last_month_salary > 5416 AND last_month_salary <= 8300 THEN 3
    WHEN last_month_salary > 8300 THEN 4
  END AS last_month_salary_quartile,
  CASE 
    WHEN number_dependents = 0 THEN 1
    WHEN number_dependents >= 1 THEN 2
  END AS number_dependents_quartile,
  CASE 
    WHEN Prestamo_inmobiliario = 0 THEN 1
    WHEN Prestamo_inmobiliario = 1 THEN 2
    WHEN Prestamo_inmobiliario >1 THEN 3
  END AS Prestamo_inmobiliario_quartile,
  CASE 
    WHEN Otro_tipo_prestamo <= 4 THEN 1
    WHEN Otro_tipo_prestamo > 4 AND Otro_tipo_prestamo <= 7 THEN 2
    WHEN Otro_tipo_prestamo > 7 AND Otro_tipo_prestamo <= 10 THEN 3
    WHEN Otro_tipo_prestamo > 10 THEN 4
  END AS Otro_tipo_prestamo_quartile,
  CASE 
    WHEN Total_prestamos <= 5 THEN 1
    WHEN Total_prestamos > 5 AND Total_prestamos <= 8 THEN 2
    WHEN Total_prestamos > 8 AND Total_prestamos <= 11 THEN 3
    WHEN Total_prestamos > 11 THEN 4
  END AS Total_prestamos_quartile,
  CASE 
            WHEN more_90_days_overdue <= 1 THEN 1
            WHEN more_90_days_overdue >1 AND more_90_days_overdue <= 3 THEN 2
            WHEN more_90_days_overdue >3 AND more_90_days_overdue <= 6 THEN 3
            WHEN more_90_days_overdue >6 THEN 4
        END AS more_90_days_overdue_quartile,
  CASE 
    WHEN using_lines_not_secured_personal_assets <= 0.03 THEN 1
    WHEN using_lines_not_secured_personal_assets > 0.03 AND using_lines_not_secured_personal_assets<= 0.15 THEN 2
    WHEN using_lines_not_secured_personal_assets > 0.15 AND using_lines_not_secured_personal_assets <= 0.55 THEN 3
    WHEN using_lines_not_secured_personal_assets > 0.55 THEN 4
  END AS using_lines_not_secured_personal_assets_quartile,
  CASE 
    WHEN debt_ratio <= 0.17 THEN 1
    WHEN debt_ratio > 0.17 AND debt_ratio<= 0.36 THEN 2
    WHEN debt_ratio > 0.36 AND debt_ratio <= 0.87 THEN 3
    WHEN debt_ratio > 0.87 THEN 4
  END AS debt_ratio_quartile,
  FROM `proyecto3-429523.Proyecto3.banco_all_data`
)
SELECT a.*, q.* EXCEPT(user_id)
FROM `proyecto3-429523.Proyecto3.banco_all_data` a
LEFT JOIN quartiles q ON a.user_id = q.user_id 
```
## 2. Riesgo Relativo y Score
Determina el riesgo relativo, convierte los datos en variables categóricas, y calcula una puntuación general. Incluye columnas para identificar el rango asociado a cada variable.

```
WITH 
  AgeRelativeRisk AS (
    SELECT age_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS age_rr,
      CASE 
        WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 1.5 THEN 1 ELSE 0
      END AS age_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY age_quartile
  ),

  SalaryRelativeRisk AS (
    SELECT 
      last_month_salary_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS salary_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 1.5 THEN 1 ELSE 0 END AS salary_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY last_month_salary_quartile
  ),

  DependentsRelativeRisk AS (
    SELECT 
      number_dependents_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS number_dependents_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 1.4 THEN 1 ELSE 0 END AS number_dependents_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY number_dependents_quartile
  ),

  TotalLoanRelativeRisk AS (
    SELECT 
      Total_prestamos_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS total_loan_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 1.5 THEN 1 ELSE 0 END AS total_loan_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY Total_prestamos_quartile
  ),

  OverdueRelativeRisk AS (
    SELECT 
      more_90_days_overdue_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS overdue_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 50 THEN 1 ELSE 0 END AS overdue_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY more_90_days_overdue_quartile
  ),

  LinesRelativeRisk AS (
    SELECT 
      using_lines_not_secured_personal_assets_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS lines_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 40 THEN 1 ELSE 0 END AS lines_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY using_lines_not_secured_personal_assets_quartile
  ),

  DebtRatioRelativeRisk AS (
    SELECT 
      debt_ratio_quartile,
      ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) AS debt_ratio_rr,
      CASE WHEN ROUND((COUNTIF(default_flag=1)/COUNT(*))/((621-COUNTIF(default_flag=1))/(34925-COUNT(*))),2) > 1.3 THEN 1 ELSE 0 END AS debt_ratio_rr_binary
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY debt_ratio_quartile
  ),

  AgeRanges AS (
    SELECT age_quartile,
      CONCAT(MIN(age),"-",MAX(age)) AS age_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY age_quartile
    ORDER BY age_quartile
  ),

  SalaryRanges AS (
    SELECT last_month_salary_quartile,
      CONCAT(MIN(last_month_salary),"-",MAX(last_month_salary)) AS salary_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY last_month_salary_quartile
    ORDER BY last_month_salary_quartile
  ),

  DependentsRanges AS (
    SELECT number_dependents_quartile,
      IF(number_dependents_quartile=1, "0", ">=1") AS dependents_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY number_dependents_quartile
    ORDER BY number_dependents_quartile
  ),

  TotalLoanRanges AS (
    SELECT Total_prestamos_quartile,
      CONCAT(MIN(Total_prestamos),"-",MAX(Total_prestamos)) AS Total_prestamos_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY Total_prestamos_quartile
    ORDER BY Total_prestamos_quartile
  ),

  OverdueRanges AS (
    SELECT more_90_days_overdue_quartile,
      CONCAT(MIN(more_90_days_overdue), "-", MAX(more_90_days_overdue)) AS overdue_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY more_90_days_overdue_quartile
    ORDER BY more_90_days_overdue_quartile
  ),

  LinesRanges AS (
    SELECT using_lines_not_secured_personal_assets_quartile,
      CONCAT(ROUND(MIN(using_lines_not_secured_personal_assets),2), "-", ROUND(MAX(using_lines_not_secured_personal_assets),2)) AS lines_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY using_lines_not_secured_personal_assets_quartile
    ORDER BY using_lines_not_secured_personal_assets_quartile
  ),

  DebtRatioRanges AS (
    SELECT debt_ratio_quartile,
      CONCAT(ROUND(MIN(debt_ratio),2), "-", ROUND(MAX(debt_ratio),2)) AS debt_ratio_ranges
    FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile`
    GROUP BY debt_ratio_quartile
    ORDER BY debt_ratio_quartile
  )

SELECT 
  a.*, 
  AgeRelativeRisk.age_rr AS age_rr, 
  AgeRelativeRisk.age_rr_binary AS age_rr_binary,
  SalaryRelativeRisk.salary_rr AS salary_rr,
  SalaryRelativeRisk.salary_rr_binary AS salary_rr_binary,
  DependentsRelativeRisk.number_dependents_rr AS number_dependents_rr,
  DependentsRelativeRisk.number_dependents_rr_binary AS number_dependents_rr_binary,
  TotalLoanRelativeRisk.total_loan_rr AS total_loan_rr,
  TotalLoanRelativeRisk.total_loan_rr_binary AS total_loan_rr_binary,
  OverdueRelativeRisk.overdue_rr AS overdue_rr,
  OverdueRelativeRisk.overdue_rr_binary AS overdue_rr_binary,
  LinesRelativeRisk.lines_rr AS lines_rr,
  LinesRelativeRisk.lines_rr_binary AS lines_rr_binary,
  DebtRatioRelativeRisk.debt_ratio_rr AS debt_ratio_rr,
  DebtRatioRelativeRisk.debt_ratio_rr_binary AS debt_ratio_rr_binary,
  age_rr_binary + salary_rr_binary + number_dependents_rr_binary + total_loan_rr_binary + overdue_rr_binary + lines_rr_binary + debt_ratio_rr_binary AS score,
  AgeRanges.age_ranges AS age_ranges,
  SalaryRanges.salary_ranges AS salary_ranges,
  DependentsRanges.dependents_ranges AS dependents_ranges,
  TotalLoanRanges.Total_prestamos_ranges AS total_loan_ranges,
  OverdueRanges.overdue_ranges AS overdue_ranges,
  LinesRanges.lines_ranges AS lines_ranges,
  DebtRatioRanges.debt_ratio_ranges AS debt_ratio_ranges

FROM `proyecto3-429523.Proyecto3.banco_all_data_quartile` a
LEFT JOIN AgeRelativeRisk ON a.age_quartile = AgeRelativeRisk.age_quartile
LEFT JOIN SalaryRelativeRisk ON a.last_month_salary_quartile = SalaryRelativeRisk.last_month_salary_quartile
LEFT JOIN DependentsRelativeRisk ON a.number_dependents_quartile = DependentsRelativeRisk.number_dependents_quartile
LEFT JOIN TotalLoanRelativeRisk ON a.Total_prestamos_quartile = TotalLoanRelativeRisk.Total_prestamos_quartile
LEFT JOIN OverdueRelativeRisk ON a.more_90_days_overdue_quartile = OverdueRelativeRisk.more_90_days_overdue_quartile
LEFT JOIN LinesRelativeRisk ON a.using_lines_not_secured_personal_assets_quartile = LinesRelativeRisk.using_lines_not_secured_personal_assets_quartile
LEFT JOIN DebtRatioRelativeRisk ON a.debt_ratio_quartile = DebtRatioRelativeRisk.debt_ratio_quartile

LEFT JOIN AgeRanges ON a.age_quartile = AgeRanges.age_quartile
LEFT JOIN SalaryRanges ON a.last_month_salary_quartile = SalaryRanges.last_month_salary_quartile
LEFT JOIN DependentsRanges ON a.number_dependents_quartile = DependentsRanges.number_dependents_quartile
LEFT JOIN TotalLoanRanges ON a.Total_prestamos_quartile = TotalLoanRanges.Total_prestamos_quartile
LEFT JOIN OverdueRanges ON a.more_90_days_overdue_quartile = OverdueRanges.more_90_days_overdue_quartile
LEFT JOIN LinesRanges ON a.using_lines_not_secured_personal_assets_quartile = LinesRanges.using_lines_not_secured_personal_assets_quartile
LEFT JOIN DebtRatioRanges ON a.debt_ratio_quartile = DebtRatioRanges.debt_ratio_quartile 
```
