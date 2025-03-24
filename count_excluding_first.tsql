-- Удаляем временную таблицу, если она существует
IF OBJECT_ID('tempdb..#temp_data') IS NOT NULL
    DROP TABLE #temp_data;

-- Создаем временную таблицу и заполняем данными
CREATE TABLE #temp_data (
    date DATE,
    count INT
);

INSERT INTO #temp_data (date, count) VALUES
('2022-05-04', 5),
('2022-05-03', 2),
('2022-07-02', 6),
('2022-12-01', 3),
('2022-10-01', 1),
('2022-10-15', 1),
('2022-10-27', 7),
('2022-07-03', 2);

-- Основной запрос
WITH FirstDays AS (
    -- Выбираем первые числа месяца
    SELECT 
        date AS original_date,
        FORMAT(date, 'dd.MM.yyyy') AS formatted_date,
        count
    FROM #temp_data
    WHERE DAY(date) = 1
),
MonthlySums AS (
    -- Суммируем значения, исключая первые числа
    SELECT
        DATEADD(MONTH, DATEDIFF(MONTH, 0, date), 0) AS month_start,
        SUM(count) AS total_count
    FROM #temp_data
    WHERE DAY(date) <> 1
    GROUP BY DATEADD(MONTH, DATEDIFF(MONTH, 0, date), 0)
)
-- Объединяем и сортируем результаты
SELECT 
    date,
    count
FROM (
    SELECT 
        formatted_date AS date,
        count,
        original_date,      -- Скрытый столбец для сортировки
        month_start = NULL  -- Скрытый столбец для совместимости UNION
    FROM FirstDays
    UNION ALL
    SELECT
        FORMAT(month_start, 'MM.yyyy') AS date,
        total_count,
        original_date = NULL,  -- Скрытый столбец для совместимости
        month_start            -- Скрытый столбец для сортировки
    FROM MonthlySums
) AS CombinedData
ORDER BY 
    COALESCE(original_date, month_start),  -- Сортировка по дате
    CASE WHEN original_date IS NOT NULL THEN 0 ELSE 1 END;  -- Порядок внутри месяца

-- Удаляем временную таблицу
DROP TABLE IF EXISTS #temp_data;