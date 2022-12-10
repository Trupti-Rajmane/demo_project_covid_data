# demo_project_covid_data

--covid deaths
select *
from angelic-archery-364115.covid_data.covid_deaths
order by 3,4

--covid vacc
select * 
from angelic-archery-364115.covid_data.covid_vaccinations
order by 3,4

--totaldeathcount
select location,population,max(cast(total_deaths as int)) as Totaldeathcount
from angelic-archery-364115.covid_data.covid_deaths
where continent is not null
group by location,population
order by Totaldeathcount desc;

--percentage population infected
select location,population,max(total_cases) as HighestInfectedcount,max((total_cases/population))*100 as percentage_population_Infected
from angelic-archery-364115.covid_data.covid_deaths
group by location,population
order by percentage_population_Infected desc;

--rolling people vaccinated
select dea.location,dea.continent,dea.population,dea.date,vac.new_vaccinations,sum(vac.new_vaccinations) over (partition by dea.location  order by dea.location,dea.date) as RollingPeopleVaccinated
from angelic-archery-364115.covid_data.covid_deaths as dea
join angelic-archery-364115.covid_data.covid_vaccinations as vac 
on dea.location = vac.location and dea.date = vac.date
where dea.continent is not null 
order by 1,2

--percentage people vacc
with popvsvac as

(
  select dea.location,dea.continent,dea.population,dea.date,vac.new_vaccinations,sum(cast(vac.    new_vaccinations       as   int)) over (partition by dea.location  order by dea.location,dea.date) as RollingPeopleVaccinated
  from angelic-archery-364115.covid_data.covid_deaths as dea
  join angelic-archery-364115.covid_data.covid_vaccinations as vac 
  on dea.location = vac.location and dea.date = vac.date
  where dea.continent is not null 
  --order by 1,2
)
select *, (RollingPeopleVaccinated/population)*100 as PercentPeopleVaccinated
from popvsvac


--percentage birth rate
select location,date,max(cast(total_deaths as int)) as total_deaths,max(cast(reproduction_rate as int)) as reproduction_rate,max(reproduction_rate)/max(cast(total_deaths as int))*100 as Percentage_Birth_Rate
from angelic-archery-364115.covid_data.covid_deaths
where continent is not null
group by location,date
order by Percentage_Birth_Rate desc;

--gobal cases
select date,sum(new_cases) as New_cases,sum(cast(new_deaths as int)) as New_deaths,sum(new_cases)/sum(cast(new_deaths as int))* 100 as Gobel_cases
from angelic-archery-364115.covid_data.covid_deaths
where continent is not null
group by date 
order by  1,2  
