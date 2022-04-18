# Курсовая работа № 2
## Аналитика. Начальный уровень

Курсовая работа состоит из двух частей – обязательной и дополнительной. **Для зачета необходимо выполнение только первой части.** Выполнение второй части может потребовать дополнительные знания Python.

- [Часть первая](#Часть-первая)
- [Часть вторая](#Часть-вторая)

## Часть первая


Перед вами стоит задача – подготовить аналитический отчет для HR-отдела. На основании проведенной аналитики предполагается составить рекомендации для отдела кадров по стратегии набора персонала, а также по взаимодействию с уже имеющимися сотрудниками.
<br><br> В базе данных лежит набор таблиц, которые содержат данные о сотрудниках вымышленной компании.
Сделайте обзор штата сотрудников компании. Составьте набор предметов исследования, а затем проверьте их на данных. Вся аналитика должна быть выполена с помощью SQL. Впоследствии данные можно визуализировать, однако финальные датафреймы для графиков также должны быть подготовлены с помощью SQL. <br><br>

Примеры гипотез:
1. Есть зависимость между `perfomance score` и тем, под чьим руководством работает сотрудник.
2. Есть зависимость между продолжительностью работы в компании и семейным положением сотрудника.
2. Есть зависимость между продолжительностью работы в компании и возрастом сотрудника.

<br><br>
Параметры для подключения следующие: хост – `dsstudents.skillbox.ru`, порт – `5432`, имя базы данных – `human_resources`, пользователь – `readonly`, пароль – `6hajV34RTQfmxhS`. Таблицы, доступные для анализа, – `hr_dataset`, `production_staff`, `recruting_costs`, `salary_grid`.

```python
# Импорт библиотек Psycopg2, Numpy, Pandas
import psycopg2
import numpy as np
import pandas as pd

# Подключение к базе данных PostgreSQL
conn = psycopg2.connect(**{'host': 'dsstudents.skillbox.ru', 
                           'port': 5432, 
                           'dbname': 'human_resources', 
                           'user': 'readonly', 
                           'password': '6hajV34RTQfmxhS'})
```

```python
# Получение информации о структуре таблицы hr_dataset
hr_df = pd.read_sql_query('select * from hr_dataset', conn).info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 310 entries, 0 to 309
Data columns (total 29 columns):
 #   Column               Non-Null Count  Dtype  
---  ------               --------------  -----  
 0   id                   310 non-null    int64  
 1   Employee Name        310 non-null    object 
 2   Employee Number      310 non-null    int64  
 3   marriedid            310 non-null    int64  
 4   maritalstatusid      310 non-null    int64  
 5   genderid             310 non-null    int64  
 6   empstatus_id         310 non-null    int64  
 7   deptid               310 non-null    int64  
 8   perf_scoreid         310 non-null    int64  
 9   age                  310 non-null    int64  
 10  Pay Rate             310 non-null    float64
 11  state                310 non-null    object 
 12  zip                  310 non-null    int64  
 13  dob                  310 non-null    object 
 14  sex                  310 non-null    object 
 15  maritaldesc          310 non-null    object 
 16  citizendesc          310 non-null    object 
 17  Hispanic/Latino      310 non-null    object 
 18  racedesc             310 non-null    object 
 19  Date of Hire         310 non-null    object 
 20  Days Employed        310 non-null    int64  
 21  Date of Termination  103 non-null    object 
 22  Reason For Term      310 non-null    object 
 23  Employment Status    310 non-null    object 
 24  department           310 non-null    object 
 25  position             310 non-null    object 
 26  Manager Name         310 non-null    object 
 27  Employee Source      310 non-null    object 
 28  Performance Score    310 non-null    object 
dtypes: float64(1), int64(11), object(17)
memory usage: 70.4+ KB
```

```python
# Получение информации о структуре таблицы production_staff
pd.read_sql_query('select * from production_staff', conn).info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 256 entries, 0 to 255
Data columns (total 16 columns):
 #   Column               Non-Null Count  Dtype  
---  ------               --------------  -----  
 0   id                   256 non-null    int64  
 1   Employee Name        209 non-null    object 
 2   Race Desc            209 non-null    object 
 3   Date of Hire         209 non-null    object 
 4   TermDate             83 non-null     object 
 5   Reason for Term      209 non-null    object 
 6   Employment Status    209 non-null    object 
 7   Department           209 non-null    object 
 8   Position             209 non-null    object 
 9   Pay                  209 non-null    object 
 10  Manager Name         209 non-null    object 
 11  Performance Score    209 non-null    object 
 12  Abutments/Hour Wk 1  208 non-null    float64
 13  Abutments/Hour Wk 2  208 non-null    float64
 14  Daily Error Rate     208 non-null    float64
 15  90-day Complaints    208 non-null    float64
dtypes: float64(4), int64(1), object(11)
memory usage: 32.1+ KB
```

```python
# Получение информации о структуре таблицы recruiting_cost
pd.read_sql_query('select * from recruiting_costs', conn).info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 22 entries, 0 to 21
Data columns (total 15 columns):
 #   Column             Non-Null Count  Dtype 
---  ------             --------------  ----- 
 0   id                 22 non-null     int64 
 1   Employment Source  22 non-null     object
 2   January            22 non-null     int64 
 3   February           22 non-null     int64 
 4   March              22 non-null     int64 
 5   April              22 non-null     int64 
 6   May                22 non-null     int64 
 7   June               22 non-null     int64 
 8   July               22 non-null     int64 
 9   August             22 non-null     int64 
 10  September          22 non-null     int64 
 11  October            22 non-null     int64 
 12  November           22 non-null     int64 
 13  December           22 non-null     int64 
 14  Total              22 non-null     int64 
dtypes: int64(14), object(1)
memory usage: 2.7+ KB
```

```python
# Получение информации о структуре таблицы salary_grid
pd.read_sql_query("select * from salary_grid", conn).info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 12 entries, 0 to 11
Data columns (total 8 columns):
 #   Column      Non-Null Count  Dtype  
---  ------      --------------  -----  
 0   id          12 non-null     int64  
 1   Position    12 non-null     object 
 2   Salary Min  12 non-null     int64  
 3   Salary Mid  12 non-null     int64  
 4   Salary Max  12 non-null     int64  
 5   Hourly Min  12 non-null     float64
 6   Hourly Mid  12 non-null     float64
 7   Hourly Max  12 non-null     float64
dtypes: float64(3), int64(4), object(1)
memory usage: 896.0+ bytes
```

## Возраст сотрудника влияет на качество его работы. Возрастные сотрудники в работе допускают больше ошибок.
```python
# Получение из базы данных информации по сотрудникам компании
sql_query = '''
    select hr.age, pr."Daily Error Rate" as daily_error_rate
    from hr_dataset hr 
    left join production_staff pr on 
        (hr."Employee Name" = pr."Employee Name") and (hr."Date of Hire" = pr."Date of Hire") and 
        (hr.position = pr."Position")'''

staff_df = pd.read_sql_query(sql_query, conn).fillna(0)

# Определение возрастных групп сотрудников компании
age_range = pd.cut(staff_df['age'], [0, 30, 50, 80])

# Вывод среднего числа ежедневных ошибок, приходящееся на каждого сотрудника для каждой возрастной группы
staff_df.pivot_table(values='daily_error_rate', index=age_range, aggfunc=np.mean)
```
![png](Images/table01.jpg)

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

```python
```

