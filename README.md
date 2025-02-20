# COVID-19 ANALYSIS SQL

### Project Overview
This project analyzes the global impact of COVID-19 from 2020-2023, focusing on key metrics such as total cases, total deaths, death rates, and infection rates across different countries. The goal is to provide a data-driven understanding of the pandemic’s effect across the globe. SQL and Power BI are used to design and transform a database for analysis and a dashboard that answers some key questions, respectively.


![dashboard pic](https://github.com/user-attachments/assets/eaaf0d03-9804-41a3-9a98-00e1ed82331c)


### Tools Used
PostgreSQL: Data cleaning, transformation, Pipelining, and preliminary analysis.

Power BI: Data modeling, visualization, and reporting.


### Data Sources
The dataset used for this project is an Excel workbook from [Our World in Data](https://ourworldindata.org/covid-deaths)

### Data Cleaning/Preparation
- 	Data importation from Excel to Postgres and Data Inspection.
- 	Created Views for visualization and reporting. 
- 	Data cleaning and formatting.
- 	Downloaded an image from a Web source and formated it.

### Data Analysis in SQL
1. What is the global death percentage?
```sql
select
  concat(
  round(
          max(total_deaths :: numeric) / max(total_cases :: numeric) *100, 
    3
  ), '%')  as Death_Percentage 
from 
  covid_data_deaths 
order by 
  Death_Percentage desc;
```

2. Show the global death percentage, the highest deaths, and highest cases
```sql
select 
  max(total_cases) sum_total_cases, 
  max(total_deaths) sum_total_deaths, 
  round(
          max(total_deaths :: numeric) / max(total_cases :: numeric), 
    3
  ) * 100 as Death_Percentage 
from 
  covid_data_deaths 
order by 
  Death_Percentage desc;
```

3. What are the top 10 countries with the highest percentage of death?
```sql
select 
  location, 
  max(total_cases) sum_total_cases, 
  max(total_deaths) sum_total_deaths, 
  round(
    max(total_deaths :: numeric) / max(total_cases :: numeric), 
    3
  ) * 100 as Death_Percentage 
from 
  covid_data_deaths 
where 
  continent is not null 
  and total_cases is not null 
  and total_deaths is not null 
group by 
  location 
order by 
  Death_Percentage desc 
limit 
  10;
```

4. What is the total death percentage in Nigeria?
```sql
select 
  'Nigeria' as location, 
  max(total_deaths) as sum_total_deaths, 
  max(total_cases) as sum_total_cases, 
  round(
    max(total_deaths :: numeric) * 100 / max(total_cases :: numeric), 
    3
  ) as total_death_percentage 
from 
  covid_data_deaths 
where 
  location = 'Nigeria';
```

5. What is the death Percentage in America in 2020?
```sql
select 
  location, 
  max(total_deaths) as sum_total_deaths, 
  max(total_cases) as sum_total_cases, 
  round(
    max(total_deaths :: numeric) * 100 / max(total_cases :: numeric), 
    3
  ) as total_death_percentage 
from 
  covid_data_deaths 
where 
  location = 'United States' --and extract(year from date) = 2020
group by 
  location;
```

6. What is the Death Percentage in China in 2020?
```sql
select 
  'China' as location, 
  max(total_deaths) as sum_total_deaths, 
  max(total_cases) as sum_total_cases, 
  round(
    max(total_deaths :: numeric) * 100 / max(total_cases :: numeric), 
    3
  ) as total_death_percentage 
from 
  covid_data_deaths 
where 
  location = 'China' 
  and extract(
    year 
    from 
      date
  ) = 2020;
```

7. What is the population rate of Covid-19 in Nigeria?
```sql
select 
  'Nigeria' as location, 
  max(total_cases) as total_cases, 
  max(population) as total_population, 
  round(
    cast(
      (
        max(total_cases) * 100.0 / nullif(
          max(population), 
          0
        )
      ) as numeric(10, 4)
    ), 
    3
  ) cases_rate 
from 
  covid_data_deaths 
where 
  location = 'Nigeria';
```

8. What is the rate of population that have COVID-19 in the United States?
```sql
select 
  'United States' as location, 
  max(total_cases) as total_cases, 
  max(population) as total_population, 
  round(
    cast(
      (
        max(total_cases) * 100.0 / nullif(
          max(population), 
          0
        )
      ) as numeric(10, 4)
    ), 
    2
  ) cases_rate 
from 
  covid_data_deaths 
where 
  location = 'United States';
```

9. What is the population rate of COVID-19 in China in 2023?
```sql
select 
  'China' as location, 
  max(total_cases) as total_cases, 
  max(population) as total_population, 
  round(
    cast(
      (
        max(total_cases) * 100.0 / nullif(
          max(population), 
          0
        )
      ) as numeric(10, 4)
    ), 
    2
  ) cases_rate 
from 
  covid_data_deaths 
where 
  location = 'China' 
  and extract(
    year 
    from 
      date
  ) = 2023 
order by 
  1, 
  2;
```

10. What are the top 10 Countries with the highest infection rate?
```sql
select 
  location, 
  (population), 
  max(total_cases) as sum_total_cases, 
  round(
    (
      max(total_cases) * 100.0 / nullif(
        max(population), 
        0
      )
    ), 
    2
  ) as highest_infection_count 
from 
  covid_data_deaths 
where 
  total_cases is not null 
group by 
  location, 
  population 
order by 
  highest_infection_count desc 
limit 
  10;
```

11. Top 10 countries with the highest COVID-19 death?
```sql
select 
  location, 
  max(total_deaths :: numeric) as sum_total_deaths 
from 
  covid_data_deaths 
where 
  total_deaths is not null 
  and continent is not null 
group by 
  location 
order by 
  sum_total_deaths desc
limit 
  10;
```

12. Top 10 countries with the lowest COVID-19 death?
```sql
select 
  location, 
  max(total_deaths :: numeric) as sum_total_deaths 
from 
  covid_data_deaths 
where 
  total_deaths is not null 
  and continent is not null 
group by 
  location 
order by 
  sum_total_deaths asc
limit 
  10;
```

13. What are the top 10 countries with the lowest COVID-19 death?
```sql
select 
  location, 
  max(total_deaths :: numeric) as sum_total_deaths 
from 
  covid_data_deaths 
where 
  total_deaths is not null 
  and continent is not null 
group by 
  location 
order by 
  sum_total_deaths asc 
limit 
  10;
```

14. Show the total population that was vaccinated in each country?
```sql
select 
  dea.continent, 
  dea.location, 
  dea.date, 
  dea.population, 
  vaccs.new_vaccinations, 
  sum(
    vaccs.new_vaccinations :: numeric
  ) over(
    partition by dea.location 
    order by 
      dea.location, 
      dea.date
  ) as tot_count_people_vaccinated 
from 
  covid_data_deaths as dea 
  join covid_data_vaccinations as vaccs on dea.location = vaccs.location 
  and dea.date = vaccs.date 
where 
  dea.continent is not null 
  and vaccs.new_vaccinations is not null 
order by 
  2, 
  3;
```

15. The total population of people fully vaccinated?
```sql
select 
  dea.location, 
  dea.date, 
  dea.population, 
  vaccs.people_fully_vaccinated, 
  sum(
    vaccs.people_fully_vaccinated :: numeric
  ) over(
    partition by dea.location 
    order by 
      dea.location, 
      dea.date
  ) as tot_count_people_Fully_vaccinated 
from 
  covid_data_deaths as dea 
  join covid_data_vaccinations as vaccs on dea.location = vaccs.location 
  and dea.date = vaccs.date 
where 
  dea.continent is not null 
  and vaccs.people_fully_vaccinated is not null 
order by 
  1, 
  2;
```

16. Which country has the highest reproductive rate?
```sql
select 
  location, 
  reproduction_rate 
from 
  covid_data_deaths 
where 
  continent is not null 
  and reproduction_rate = (
    select 
      max(reproduction_rate) 
    from 
      covid_data_deaths
  ) 
order by 
  reproduction_rate desc;
```

17. Which country has the lowest reproductive rate?
```sql
select 
  location, 
  reproduction_rate 
from 
  covid_data_deaths 
where 
  continent is not null 
  and reproduction_rate = (
    select 
      min(reproduction_rate) 
    from 
      covid_data_deaths
  ) 
order by 
  reproduction_rate desc;
```

18. What is the total number of ICU patients across the globe in 2020?
```sql
select 
  sum(icu_patients :: numeric) total_icu_patients 
from 
  covid_data_deaths 
where 
  continent is not null 
  and extract(
    year 
    from 
      date
  ) = 2020;
```

19. Which country has the highest number of ICU patients in 2020?
```sql
select 
  location, 
  sum(icu_patients :: numeric) total_icu_patients 
from 
  covid_data_deaths 
where 
  continent is not null 
  and extract(
    year 
    from 
      date
  ) = 2020 
  and icu_patients is not null 
group by 
  location 
order by 
  total_icu_patients desc 
limit 
  1;
```

20. Show the top 5 countries with the highest positive rate of COVID-19 when tested?
```sql
select 
  location, 
  max(positive_rate) total_positive_rate 
from 
  covid_data_vaccinations 
where 
  positive_rate is not null 
group by 
  location 
order by 
  total_positive_rate desc 
limit 
  5; 
```

21. identify the African countries with the highest life expectancy and human development index, along with their respective populations
```sql 
select 
  distinct on (det.location) det.location, 
  det.date, 
  det.population, 
  det.continent, 
  max(vac.human_development_index), 
  max(vac.life_expectancy) 
from 
  covid_data_deaths as det 
  join covid_data_vaccinations as vac on det.continent = vac.continent 
  and det.date = vac.date 
where 
  det.continent = 'Africa' 
  and vac.human_development_index is not null 
group by 
  det.population, 
  det.date, 
  det.location, 
  det.continent, 
  vac.human_development_index, 
  vac.life_expectancy 
order by 
  det.location, 
  vac.life_expectancy desc, 
  vac.human_development_index desc;
```

22. identify the European countries with the highest life expectancy and human development index, 
along with their respective populations.
```sql
select 
  distinct on (det.location) det.location, 
  det.date, 
  det.population, 
  det.continent, 
  max(vac.human_development_index), 
  max(vac.life_expectancy) 
from 
  covid_data_deaths as det 
  join covid_data_vaccinations as vac on det.continent = vac.continent 
  and det.date = vac.date 
where 
  det.continent = 'Europe' 
  and vac.human_development_index is not null 
group by 
  det.population, 
  det.date, 
  det.location, 
  det.continent, 
  vac.human_development_index, 
  vac.life_expectancy 
order by 
  det.location, 
  vac.life_expectancy desc, 
  vac.human_development_index desc;
```

### Data Visualization in Power BI
1.	What is the global death percentage?
2.	What are the top 10 countries with the highest percentage of death?
3.	Top 10 countries with the lowest COVID-19 death?
4.	What are the top 10 Countries with the highest infection rate?
5.	Show the global death percentage, the highest deaths, and the highest cases
6.	Top 10 countries with the highest COVID-19 death?

### Results
The results show that:
1.	The global death percentage is 0.904%, this shows the percentage of the global population that from COVID-19 from 2020-2023.
2.	Over 7 million people died from COVID-19 globally.
3.	About 770 million people had cases of COVID-19 globally.
4.	The United States (1.14m), Brazil (0.70m), and India (0.53m) amongst others have the most deaths.
5.	Nauru, Tuvalu, Cook Islands, etc., have minimal deaths (1-8 cases).
6.	Yemen with 18.1% leads to a high death rate ahead of others.
7.	Cyprus (73.76%), San Marino (73.31%), Brunei (69.51%), and others show high infection rates.
### Recommendation
From the analysis, we recommend the following;
1.	Countries with high death rates (Yemen, Sudan, Syria, Somalia) have high death rates because of the wars going on in the countries and lack of proper medical infrastructure, therefore, there is an urgent need investment in healthcare infrastructure, improved access to medical supplies, and emergency response systems to reduce fatalities in future pandemics.
2.	Countries with high infection rates (Cyprus, San Marino, Brunei) should enhance early detection, testing, and contact tracing to control outbreaks faster and reduce the speed.
3.	Nations with deaths like (the United States, Brazil, and India) should promote booster vaccinations, mask mandates, and public awareness programs to prevent severe cases.
4.	Countries must share data, best practices, and research findings to create a unified global response against pandemics. WHO should standardize pandemic protocols to ensure equitable access to treatments and vaccines worldwide.





