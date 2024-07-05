# Анализ данных о фондах и инвестициях

**Задания:** [№1](#задание-1), [№2](#задание-2), [№3](#задание-3), [№4](#задание-4), [№5](#задание-5), 
[№6](#задание-6), [№7](#задание-7), [№8](#задание-8), [№9](#задание-9), [№10](#задание-10), [№11](#задание-11),
[№12](#задание-12), [№13](#задание-13), [№14](#задание-14), [№15](#задание-15), [№16](#задание-16), [№17](#задание-17),
[№18](#задание-18), [№19](#задание-19), [№20](#задание-20), [№21](#задание-21), [№22](#задание-22), [№23](#задание-23)

## Задание №1 
Отобразите все записи из таблицы `company` по компаниям, которые закрылись.

```sql
SELECT *
FROM company
WHERE status LIKE 'closed'
```
```text
| id     | name                              | category_code    | status | founded_at | closed_at  | domain                    | network_username | country_code | investment_rounds | funding_rounds | funding_total | milestones | created_at          | updated_at          |
|--------|-----------------------------------|------------------|--------|------------|------------|---------------------------|------------------|--------------|-------------------|----------------|---------------|------------|---------------------|---------------------|
| 10012  | moviestring.com                   | games_video      | closed | 2008-08-22 | 2010-01-01 | moviestring.com           |                  |              | 0                 | 0              | 0             | 0          | 2008-08-24 23:07:26 | 2010-10-01 00:12:22 |
| 1006   | Youlicit                          | web              | closed | 2006-09-01 | 2010-05-22 | youlicit.com              | youlicit         | USA          | 0                 | 1              | 0             | 0          | 2007-10-02 09:45:00 | 2013-10-19 10:39:54 |
| 10062  | Workstir                          | web              | closed |            | 2011-12-17 | workstir.com              |                  | USA          | 0                 | 1              | 0             | 2          | 2008-08-25 18:26:25 | 2013-10-16 09:57:06 |
| 10092  | FairSoftware                      | web              | closed | 2007-01-01 | 2012-07-24 | fairsoftware.net          |                  | USA          | 0                 | 1              | 100000        | 1          | 2008-08-25 23:41:44 | 2013-10-15 04:33:46 |
| 101    | SellABand                         | games_video      | closed | 2006-08-01 | 2010-02-23 | sellaband.com             | sellaband        | DEU          | 0                 | 1              | 5e+06         | 5          | 2007-07-04 05:29:56 | 2010-04-29 21:39:39 |
...
```


## Задание №2
Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы `company`. 
Отсортируйте таблицу по убыванию значений в поле `funding_total`.
```sql
SELECT funding_total
FROM company
WHERE category_code = 'news' AND country_code =  'USA'
ORDER BY funding_total DESC
```
```text
| funding_total |
|---------------|
| 6.22553e+08   |
| 2.5e+08       |
| 1.605e+08     |
| 1.28e+08      |
| 1.265e+08     |
| 7e+07         |
| 6.9e+07       |
...
```


## Задание №3
Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, 
которые осуществлялись только за наличные с 2011 по 2013 год включительно.

```sql
SELECT SUM(price_amount)
FROM acquisition
WHERE CAST(acquired_at AS DATE) BETWEEN '2011-01-01' AND '2013-12-31'
AND term_code = 'cash'
```
```text
| sum           |
|---------------|
| 1.37762e+11   |
```


## Задание №4
Отобразите имя, фамилию и названия аккаунтов людей в поле `network_username`, 
у которых названия аккаунтов начинаются на `'Silver'`.

```sql
SELECT first_name,
       last_name,
       network_username
FROM people
WHERE network_username LIKE 'Silver%'
```
```text
| first_name | last_name | network_username |
|------------|-----------|------------------|
| Rebecca    | Silver    | SilverRebecca    |
| Silver     | Teede     | SilverMatrixx    |
| Mattias    | Guilotte  | Silverreven      |
```


## Задание №5
Выведите на экран всю информацию о людях, у которых названия аккаунтов в поле `network_username` содержат 
подстроку `'money'`, а фамилия начинается на `'K'`.

```sql
SELECT *
FROM people
WHERE network_username LIKE '%money%'
AND last_name LIKE 'K%'
```
```text
| id    | first_name | last_name | company_id | network_username | created_at          | updated_at          |
|-------|------------|-----------|------------|------------------|---------------------|---------------------|
| 63081 | Gregory    | Kim       |            | gmoney75         | 2010-07-13 03:46:28 | 2011-12-12 22:01:34 |
```


## Задание №6
Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, 
зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. 
Отсортируйте данные по убыванию суммы.

```sql
SELECT  
    country_code,
    SUM(funding_total)      
FROM company
GROUP BY country_code
ORDER BY SUM(funding_total) DESC
```
```text
| country_code | sum         |
|--------------|-------------|
| USA          | 3.10588e+11 |
| GBR          | 1.77056e+10 |
|              | 1.08559e+10 |
| CHN          | 1.06897e+10 |
| CAN          | 9.86636e+09 |
| IND          | 6.14141e+09 |
| DEU          | 5.76577e+09 |
...
```


## Задание №7
Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций,
привлечённых в эту дату.

Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и 
не равно максимальному значению.

```sql
SELECT funded_at,
      MAX(raised_amount),
      MIN(raised_amount)
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount) != 0 AND MIN(raised_amount) != MAX(raised_amount)
```
```text
| funded_at  | max         | min         |
|------------|-------------|-------------|
| 2012-08-22 | 7.5e+07     | 40000       |
| 2010-07-25 | 9e+06       | 3.27825e+06 |
| 2002-03-01 | 8.95915e+06 | 2.84418e+06 |
| 2010-10-11 | 2e+08       | 28000       |
| 2007-01-18 | 2.3e+07     | 5.5e+06     |
...
```


## Задание №8
Создайте поле с категориями:
- Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию `high_activity`.
- Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию `middle_activity`.
- Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию `low_activity`.
Отобразите все поля таблицы fund и новое поле с категориями.

```sql
SELECT *,
  CASE 
    WHEN invested_companies >= 100 THEN 'high_activity'
    WHEN invested_companies >= 20 THEN 'middle_activity'
    WHEN invested_companies < 20 THEN 'low_activity'
  END  
FROM fund
```
```text
| id    | name                                     | founded_at | domain                              | network_username | country_code | investment_rounds | invested_companies | milestones | created_at          | updated_at          | case            |
|-------|------------------------------------------|------------|-------------------------------------|------------------|--------------|-------------------|--------------------|------------|---------------------|---------------------|-----------------|
| 13131 |                                          |            |                                     |                  |              | 0                 | 0                  | 0          | 2013-08-19 18:46:55 | 2013-08-19 19:55:07 | low_activity    |
| 1     | Greylock Partners                        | 1965-01-01 | greylock.com                        | greylockvc       | USA          | 307               | 196                | 0          | 2007-05-25 20:18:23 | 2012-12-27 00:42:24 | high_activity   |
| 10    | Mission Ventures                         | 1996-01-01 | missionventures.com                 |                  | USA          | 58                | 33                 | 0          | 2007-06-05 05:24:58 | 2013-10-10 22:06:31 | middle_activity |
| 100   | Kapor Enterprises, Inc.                  |            | kei.com                             |                  | USA          | 2                 | 1                  | 0          | 2007-07-12 09:42:21 | 2008-11-21 05:41:53 | low_activity    |
| 1000  | Speed Ventures                           |            |                                     |                  |              | 0                 | 0                  | 1          | 2008-04-13 23:52:27 | 2008-12-10 09:37:18 | low_activity    |
```


## Задание №9
Для каждой из категорий, назначенных в предыдущем задании, посчитайте округлённое до ближайшего целого числа 
среднее количество инвестиционных раундов, в которых фонд принимал участие. Выведите на экран категории и среднее 
число инвестиционных раундов. Отсортируйте таблицу по возрастанию среднего.

```sql
SELECT 
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity,
       ROUND(AVG(investment_rounds))
FROM fund
GROUP BY activity
ORDER BY ROUND(AVG(investment_rounds))
```
```text
| activity        | round |
|-----------------|-------|
| low_activity    | 2     |
| middle_activity | 51    |
| high_activity   | 252   |
```


## Задание №10
Проанализируйте, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. 

Для каждой страны посчитайте минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой 
страны, основанные с 2010 по 2012 год включительно. Исключите страны с фондами, у которых минимальное число компаний, 
получивших инвестиции, равно нулю. 

Выгрузите десять самых активных стран-инвесторов: отсортируйте таблицу по среднему количеству компаний 
от большего к меньшему. Затем добавьте сортировку по коду страны в лексикографическом порядке.

```sql
SELECT country_code,
       MIN(invested_companies),
       MAX(invested_companies),
       AVG(invested_companies)       
FROM fund
WHERE CAST(founded_at AS DATE) BETWEEN '2010-01-01' AND '2012-12-31'
GROUP BY country_code
HAVING MIN(invested_companies) > 0
ORDER BY AVG(invested_companies) DESC, country_code
LIMIT 10;
```
```text
| country_code | min | max | avg     |
|--------------|-----|-----|---------|
| BGR          | 25  | 35  | 30      |
| CHL          | 29  | 29  | 29      |
| UKR          | 8   | 10  | 9       |
| LTU          | 5   | 5   | 5       |
| IRL          | 4   | 5   | 4.5     |
| KEN          | 3   | 3   | 3       |
| LBN          | 3   | 3   | 3       |
| MUS          | 3   | 3   | 3       |
| JPN          | 1   | 6   | 2.83333 |
| HKG          | 2   | 3   | 2.66667 |
```


## Задание №11
Отобразите имя и фамилию всех сотрудников стартапов. Добавьте поле с названием учебного заведения, 
которое окончил сотрудник, если эта информация известна.

```sql
SELECT p.first_name,
       p.last_name,
       e.instituition
FROM  people AS p
LEFT JOIN education AS e ON p.id = e.person_id
```
```text
| first_name    | last_name             | instituition                                                     |
|---------------|-----------------------|------------------------------------------------------------------|
| John          | Green                 | Washington University, St. Louis                                 |
| John          | Green                 | Boston University                                                |
| David         | Peters                | Rice University                                                  |
| Dan           | Birdwhistell          | University of Cambridge                                          |
...
```


## Задание №12
Для каждой компании найдите количество учебных заведений, которые окончили её сотрудники. Выведите название компании 
и число уникальных названий учебных заведений. Составьте топ-5 компаний по количеству университетов.

```sql
SELECT c.name,
       count(distinct e.instituition)     
FROM company AS c
left join people as p on c.id = p.company_id
left join education as e on p.id = e.person_id
group by c.id, c.name
order by count(distinct e.instituition) desc
limit 5
```
```text
| name              | count |
|-------------------|-------|
| Google            | 167   |
| Yahoo!            | 115   |
| Microsoft         | 111   |
| Knight Foundation | 74    |
| Comcast           | 66    |
```


## Задание №13
Составьте список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.

```sql
select distinct c.name
from company as c
left join funding_round as fr on c.id = fr.company_id
where status = 'closed'
      and is_first_round = 1
      and is_last_round = 1
group by c.id, c.name
```
```text
| name                                    |
|-----------------------------------------|
| 10BestThings                            |
| 11i Solutions                           |
| 169 ST.                                 |
| 1bib                                    |
| 1Cast                                   |
| 1DayMakeover                            |
...
```


## Задание №14
Составьте список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.

```sql
with nc as (
    select c.id
    from company as c
    left join funding_round as fr on c.id = fr.company_id
    where status = 'closed'
          and is_first_round = 1
          and is_last_round = 1
    group by c.id
)
select distinct p.id
from people as p
inner join nc on nc.id = p.company_id
```
```text
| id    |
|-------|
| 62    |
| 97    |
| 98    |
| 225   |
| 226   |
...
```


## Задание №15
Составьте таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, 
которое окончил сотрудник.

```sql
with nc as (
    select c.id
    from company as c
    left join funding_round as fr on c.id = fr.company_id
    where status = 'closed'
        and is_first_round = 1
        and is_last_round = 1
    group by c.id
),
ncp as (        
    select distinct p.id
    from people as p
    inner join nc on nc.id = p.company_id
)
select distinct ncp.id,
       e.instituition
from ncp
inner join education as e on e.person_id = ncp.id
```
```text
| id    | instituition                                                     |
|-------|------------------------------------------------------------------|
| 349   | AKI                                                              |
| 349   | ArtEZ Hogeschool voor de Kunsten                                 |
| 349   | Rijks Akademie                                                   |
| 699   | Imperial College                                                 |
| 779   | Harvard University                                               |
...
```


## Задание №16
Посчитайте количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитывайте, 
что некоторые сотрудники могли окончить одно и то же заведение дважды.

```sql
with nc as (
    select c.id
    from company as c
    left join funding_round as fr on c.id = fr.company_id
    where status = 'closed'
        and is_first_round = 1
        and is_last_round = 1
    group by c.id
),
ncp as (        
    select distinct p.id
    from people as p
    inner join nc on nc.id = p.company_id
)
select  ncp.id,
       count( e.instituition)
from ncp
inner join education as e on e.person_id = ncp.id
group by  ncp.id
```
```text
| id     | count |
|--------|-------|
| 349    | 3     |
| 699    | 1     |
| 779    | 2     |
| 968    | 1     |
| 972    | 1     |
...
```


## Задание №17
Дополните предыдущий запрос и выведите среднее число учебных заведений (всех, не только уникальных), 
которые окончили сотрудники разных компаний. Нужно вывести только одну запись, группировка здесь не понадобится.

```sql
with nc as (
    select c.id
    from company as c
    left join funding_round as fr on c.id = fr.company_id
    where status = 'closed'
        and is_first_round = 1
        and is_last_round = 1
    group by c.id
),
ncp as (        
    select distinct p.id
    from people as p
    inner join nc on nc.id = p.company_id
),
ncpe as (
     select  ncp.id,
             count(e.instituition) as cnt
    from ncp
    inner join education as e on e.person_id = ncp.id
    group by  ncp.id
) 
select avg(ncpe.cnt) as avg_cnt
from ncpe
```
```text
| avg_cnt |
|---------|
| 1.41509 |

```


## Задание №18
Напишите похожий запрос: выведите среднее число учебных заведений (всех, не только уникальных), 
которые окончили сотрудники Socialnet.

```sql
with pe as (
    select p.id, 
           count(e.instituition) as cnt
    from people as p
    left join company as c on p.company_id = c.id
    left join education as e on e.person_id = p.id
    where c.name = 'Socialnet' and  e.instituition is not null
    group by p.id
)
select avg(pe.cnt)
from pe
```
```text
| avg     |
|---------|
| 1.51111 |
```


## Задание №19
Составьте таблицу из полей:
- `name_of_fund` — название фонда;
- `name_of_company` — название компании;
- `amount` — сумма инвестиций, которую привлекла компания в раунде.

В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, 
а раунды финансирования проходили с 2012 по 2013 год включительно.

```sql
select f.name as name_of_fund,
       c.name as name_of_company,
       fr.raised_amount as amount
from company as c
inner join investment as i on c.id = i.company_id
inner join fund as f on i.fund_id = f.id
inner join funding_round as fr on fr.id = i.funding_round_id
where c.milestones > 6
and cast(fr.funded_at as date) BETWEEN '2012-01-01' and '2013-12-31'
```
```text
| name_of_fund             | name_of_company | amount      |
|--------------------------|-----------------|-------------|
| Advance Publication      | Gigya           | 1.53e+07    |
| Mayfield Fund            | Gigya           | 1.53e+07    |
| Benchmark                | Gigya           | 1.53e+07    |
| DAG Ventures             | Gigya           | 1.53e+07    |
| Mitsui Global Investment | OpenX           | 2.50112e+07 |
| Accel Partners           | OpenX           | 2.50112e+07 |
...
```


## Задание №20
Выгрузите таблицу, в которой будут такие поля:
- название компании-покупателя;
- сумма сделки;
- название компании, которую купили;
- сумма инвестиций, вложенных в купленную компанию;
- доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.

Не учитывайте те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, 
исключите такую компанию из таблицы. 

Отсортируйте таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в 
лексикографическом порядке. Ограничьте таблицу первыми десятью записями.

```sql
select c.name as acquiring_company,
       a.price_amount,
       cb.name as acquired_company,
       cb.funding_total,
       round(a.price_amount / cb.funding_total) as price_rate
from acquisition as a
left join company as c on a.acquiring_company_id = c.id
left join company as cb on a.acquired_company_id = cb.id
where a.price_amount > 0
and cb.funding_total > 0
order by a.price_amount desc, acquired_company
limit 10
```
```text
| acquiring_company      | price_amount | acquired_company                          | funding_total | price_rate |
|------------------------|--------------|-------------------------------------------|---------------|------------|
| Microsoft              | 8.5e+09      | Skype                                     | 7.6805e+07    | 111        |
| Scout Labs             | 4.9e+09      | Varian Semiconductor Equipment Associates | 4.8e+06       | 1021       |
| Broadcom               | 3.7e+09      | Aeluros                                   | 7.97e+06      | 464        |
| Broadcom               | 3.7e+09      | NetLogic Microsystems                     | 1.88527e+08   | 20         |
| Level 3 Communications | 3e+09        | Global Crossing                           | 4.1e+07       | 73         |
...
```


## Задание №21
Выгрузите таблицу, в которую войдут названия компаний из категории `social`, получившие финансирование 
с 2010 по 2013 год включительно. Проверьте, что сумма инвестиций не равна нулю. Выведите также номер месяца, 
в котором проходил раунд финансирования.

```sql
select c.name,
        EXTRACT(MONTH FROM CAST(funded_at AS TIMESTAMP))
from company as c 
left join funding_round as fr on c.id = fr.company_id
where c.category_code = 'social'
and cast(funded_at as date) between '2010-01-01' and '2013-12-31'
and raised_amount > 0
```
```text
| name                                  | extract |
|---------------------------------------|---------|
| Klout                                 | 1       |
| WorkSimple                            | 3       |
| HengZhi                               | 1       |
| Network                               | 1       |
| SocialGO                              | 1       |
...
```


## Задание №22
Отберите данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. 
Сгруппируйте данные по номеру месяца и получите таблицу, в которой будут поля:
- номер месяца, в котором проходили раунды;
- количество уникальных названий фондов из США, которые инвестировали в этом месяце;
- количество компаний, купленных за этот месяц;
- общая сумма сделок по покупкам в этом месяце.

```sql
with acq as (
    select count(a.acquired_company_id) as acquired_companies,
           sum(a.price_amount) as total_price,
           extract(month from cast(a.acquired_at as timestamp)) as month
    from acquisition as a
    where cast(acquired_at as date) between '2010-01-01' and '2013-12-31'
    group by month
),    
fnd as (
    select extract(month from cast(fr.funded_at as timestamp)) as month,
           count(distinct f.name) as fund_cnt
    from fund as f
    left join investment as i on i.fund_id = f.id
    left join funding_round as fr on i.funding_round_id = fr.id
    where country_code = 'USA'
    and cast(fr.funded_at as date) between '2010-01-01' and '2013-12-31'
    group by month
)
select fnd.month, fnd.fund_cnt, acq.acquired_companies, acq.total_price
from fnd
join acq on acq.month = fnd.month
```
```text
| month | fund_cnt | acquired_companies | total_price |
|-------|----------|--------------------|-------------|
| 1     | 815      | 600                | 2.71083e+10 |
| 2     | 637      | 418                | 4.13903e+10 |
| 3     | 695      | 458                | 5.95016e+10 |
| 4     | 718      | 411                | 3.03837e+10 |
| 5     | 695      | 532                | 8.60122e+10 |
...
```


## Задание №23
Составьте сводную таблицу и выведите среднюю сумму инвестиций для стран, в которых есть стартапы, 
зарегистрированные в 2011, 2012 и 2013 годах. Данные за каждый год должны быть в отдельном поле. 
Отсортируйте таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.

```sql
with y2011 as (
    select avg(funding_total) as avg_funding_total,
           country_code    
    from company
    where cast(founded_at as date) between '2011-01-01' and '2011-12-31'
    group by country_code
),
y2012 as (
    select avg(funding_total) as avg_funding_total,
           country_code    
    from company
    where cast(founded_at as date) between '2012-01-01' and '2012-12-31'
    group by country_code
),
y2013 as (
    select avg(funding_total) as avg_funding_total,
           country_code    
    from company
    where cast(founded_at as date) between '2013-01-01' and '2013-12-31'
    group by country_code
)    
select y2011.country_code,
       y2011.avg_funding_total as avg_y2011,
       y2012.avg_funding_total as avg_y2012,
       y2013.avg_funding_total as avg_y2013
from y2011
join y2012 on y2011.country_code = y2012.country_code
join y2013 on y2012.country_code = y2013.country_code
order by avg_y2011 desc
```
```text
| country_code | avg_y2011   | avg_y2012   | avg_y2013   |
|--------------|-------------|-------------|-------------|
| PER          | 4e+06       | 41000       | 25000       |
| USA          | 2.24396e+06 | 1.20671e+06 | 1.09336e+06 |
| HKG          | 2.18078e+06 | 226227      | 0           |
| PHL          | 1.75e+06    | 4218.75     | 2500        |
| ARE          | 1.718e+06   | 197222      | 35333.3     |
| JPN          | 1.66431e+06 | 674720      | 50000       |
```

