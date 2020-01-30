# Dalmatia2020ThesisCode
Codes used for Master Thesis. Author: Tomislav Grcic

# Trajectories averages
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import matplotlib.pyplot as plt
%matplotlib inline

df_traj = pd.read_csv('information_trajectories.csv')
df_traj.head()

f = '%Y-%m-%d %H:%M:%S'
def convert_date(date): return datetime.strptime(str(date),f)

df_traj['startdate'] = df_traj['startdate'].apply(convert_date)
df_traj['enddate'] = df_traj['enddate'].apply(convert_date)
print(df_traj.dtypes)

years = list(range(2009,2019))
seasons = [('winter',(1,12),(28,2)),('spring',(1,3),(31,5)),('summer',(1,6),(31,8)),('autumn',(1,9),(30,11))]
colors = ['#66c2a5','#fc8d62','#8da0cb','#e78ac3']

data = {}

for year in years:
    for s in seasons:
        season = s[0]
        data_season = data.get(season,[])
        
        
        if season=='winter':
            from_date = datetime(year=year-1,month=s[1][1],day=s[1][0])
        else:
            from_date = datetime(year=year,month=s[1][1],day=s[1][0])
        to_date = datetime(year=year,month=s[2][1],day=s[2][0],hour=23,minute=59,second=59)
        
        # print(from_date,to_date)
        
        df_season = df_traj[(from_date<=df_traj['startdate']) & (df_traj['enddate']<=to_date)]
        avg_length = np.mean(df_season['length'])
        avg_duration = np.mean(df_season['duration'])
        
        data_season.append((avg_length,avg_duration))
        data[season] = data_season
        
    
fig, ax = plt.subplots(figsize=(16, 6))
fontsize = 16
plt.rcParams.update({'font.size': fontsize})



for i, s in enumerate(seasons):
    season = s[0]
    data_season = data[season]
    data_length = [d[0]/1000 for d in data_season]
    plt.plot(years,data_length,colors[i],label=season)
    
ax.set_xlabel('Year')
ax.set_ylabel('Average trajectory length (km)')
ax.set_xticks(years)
ax.set_xticklabels(years)
ax.legend(ncol=4)

plt.savefig(f'plots/average_trajectory_length.png',dpi=300,bbox_inches='tight')
plt.show()

fig, ax = plt.subplots(figsize=(16, 6))
fontsize = 16
plt.rcParams.update({'font.size': fontsize})



for i, s in enumerate(seasons):
    season = s[0]
    data_season = data[season]
    data_duration = [d[1]/3600/24 for d in data_season]
    plt.plot(years,data_duration,colors[i],label=season)
    
ax.set_xlabel('Year')
ax.set_ylabel('Average trajectory duration (d)')
ax.set_xticks(years)
ax.set_xticklabels(years)
ax.legend(ncol=4)

plt.savefig(f'plots/average_trajectory_duration.png',dpi=300,bbox_inches='tight')
plt.show()
