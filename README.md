# Covid19 Deaths and Vaccination Analysis

For this Analysis, I have used Covid19 dataset from [ourworldindata.com](https://ourworldindata.org/coronavirus) and bifurcated the dataset into vaccination and death datasets.

```sql
-- Selecting the Deaths table
select * 
from PortfolioProject..Death
where continent is not null
order by 3,4
```

### 1. Looking at Total Cases Vs Total Deaths

```sql
-- DeathRate Overal
	
Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths,
SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From PortfolioProject..Death
where continent is not null
order by 1,2

```

![image](https://user-images.githubusercontent.com/103417972/213464656-bc911ccd-f103-4d2b-b6b4-0287fa88cd02.png)



### 2.  Death Count by Continent

```sql
Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
From PortfolioProject..Death
Where continent is null 
and location not in ('World', 'European Union', 'International', 'High income',
'Upper middle income','Low income', 'Lower middle income')
Group by location
order by TotalDeathCount desc
```
![image](https://user-images.githubusercontent.com/103417972/213466130-80806a2b-5c89-46f2-84de-8aa7700ffca8.png)




### 3. Percentage of Population which had covid

```sql
Select Location, Population, MAX(total_cases) as HighestInfectionCount, 
Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..Death
Group by Location, Population
order by PercentPopulationInfected desc
```
![image](https://user-images.githubusercontent.com/103417972/213466866-48fd37f9-97da-4d75-89da-18a600ff8006.png)




### 4. Highest Infection Count
```sql
Select Location, Population,date, MAX(total_cases) as HighestInfectionCount, 
Max((total_cases/population))*100 as PercentPopulationInfected
From PortfolioProject..Death
Group by Location, Population, date
order by PercentPopulationInfected desc
```

![image](https://user-images.githubusercontent.com/103417972/213467215-ab1c734c-639f-4074-a26b-2df75087c259.png)



### 5. Rolling People Vaccinated and % of Population Vaccinated

```sql
with PopVsVac  as (
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(Convert(bigint,vac.new_vaccinations)) 
OVER( partition by dea.location Order by dea.location, dea.date) as RollingPeopleVaccinated
from PortfolioProject..Death as dea
join PortfolioProject..Vaccination as vac
on dea.location = vac.location and dea.date = vac.date
where dea.continent is not null
)

select *, (RollingPeopleVaccinated/population)* 100 as RollingPeopleVaccinatedInPercent  from PopVsVac
```
![image](https://user-images.githubusercontent.com/103417972/213474226-a1d9da2a-76e5-48fb-8e2d-7177dc8993ec.png)


I have created a Dashboard for above queries. [here](https://public.tableau.com/views/CovidDashboardTutorial_16744713624890/Dashboard1?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link)



## Vaccination Data Analysis

I have created a Viz using Tablue 

![image](https://user-images.githubusercontent.com/103417972/213723827-eee3a996-b15c-44d3-9476-f370d47db0ab.png)

Find the viz here -> [Covid Vaccination Visualization](https://public.tableau.com/views/CovidVaccinationDashboard_16742249419540/GlobalVaccineTracker?:language=en-US&:display_count=n&:origin=viz_share_link)
