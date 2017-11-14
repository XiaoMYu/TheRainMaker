
# FEMA Data Analysis - Susan Lee, Ebony Plummer, Xiao Yu

Every year, the US government allocates federal money to the FEMA Disaster Relief Fund. However, National Flood Insurance Program (NFIP) has been in $25 billions of dollars in debt. As the intensity of storms, fires, and hurricanes have increased over the year, more assistance will be needed to provide relief for Americans who are affected by current and future natural disasters. Therefore, in this analysis, we will be analyzing, what has been the weather and natural disaster trend over the past decades? What has our government, Federal Emergency Management Agency (FEMA) specifically, have done to combat natural disasters?


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import datetime
import seaborn as sns
```


```python
fema_claims_xl = pd.ExcelFile("Data/Yearly Paid Claims_FEMA.xlsx")
fema_funding_xl = pd.ExcelFile("Data/Yearly FEMA funding.xlsx")
disasterinci = pd.ExcelFile("Data/FEMA_Disaster incidents.xlsx")
precip_df = pd.read_csv("Data/heavy_precip.csv")
temp_data = pd.read_csv("Temperature Anomalies/Resources/Temperature_fig-1.csv")
disasterdec = pd.ExcelFile("Data/FEMA_Disaster Declarations.xlsx")
```


```python
#The total number of claims closed with payment for the given year.
#The total dollar amount paid (in billions) on closed claims for the given year.
nfip_pd = fema_claims_xl.parse("NFIP")
nfip_df = nfip_pd.groupby(["Year"]).sum()
nfip_df.reset_index(level=0, inplace=True)
nfip = nfip_df.rename(columns = {'NumberOfClaimsClosedWithPayment':'Number of Claims Closed'})
nfip = nfip.rename(columns = {'TotalPaid':'Total Paid on Closed Claims'})
nfip['Year'] = nfip['Year'].astype(int)
nfip['Total Paid on Closed Claims'] = nfip['Total Paid on Closed Claims']/1000000000
nfip.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Number of Claims Closed</th>
      <th>Total Paid on Closed Claims</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1996</td>
      <td>46849.0</td>
      <td>0.635417</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1997</td>
      <td>27063.0</td>
      <td>0.414132</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1998</td>
      <td>52074.0</td>
      <td>0.706868</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1999</td>
      <td>43385.0</td>
      <td>0.563194</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2000</td>
      <td>14718.0</td>
      <td>0.180638</td>
    </tr>
  </tbody>
</table>
</div>




```python
#The number of Flooding Episdoes for the Year.
noaa_df = fema_claims_xl.parse("NOAA")
noaa_pd = noaa_df.groupby(["Year"]).sum()
noaa_pd.pop("Lat")
noaa_pd.pop("Lon")
noaa = noaa_pd.rename(columns = {'NumEpisodes':'Flooding Episodes'})
noaa.reset_index(level=0, inplace=True)
noaa['Year'] = noaa['Year'].astype(int)
noaa.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Flooding Episodes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1996</td>
      <td>5633</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1997</td>
      <td>4658</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1998</td>
      <td>5461</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1999</td>
      <td>3873</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2000</td>
      <td>3536</td>
    </tr>
  </tbody>
</table>
</div>




```python
#FEMA Funding in billions of Real 2010 Dollars
fema_funding = fema_funding_xl.parse("Data Source-CRS Report")
fema_funding.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Administration Request</th>
      <th>Enacted Appropriation</th>
      <th>Emergency Supplemental</th>
      <th>Total Enacted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2000</td>
      <td>3.521</td>
      <td>3.521</td>
      <td>0.000</td>
      <td>3.521</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2001</td>
      <td>3.584</td>
      <td>1.964</td>
      <td>0.000</td>
      <td>1.964</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2002</td>
      <td>1.660</td>
      <td>0.805</td>
      <td>0.000</td>
      <td>0.805</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2003</td>
      <td>2.185</td>
      <td>0.948</td>
      <td>1.690</td>
      <td>2.638</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2004</td>
      <td>2.258</td>
      <td>2.078</td>
      <td>2.555</td>
      <td>4.633</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Extreme One-Day Precipitation Events:  
#percentage of the land area of the contiguous 48 states where a much greater than normal portion 
#of total annual precipitation has come from extreme single-day precipitation events.
precip = precip_df[(precip_df['Year'] >= 1996) & (precip_df['Year'] <= 2015)]
precip.pop("9-year moving average")
precip.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Index value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>86</th>
      <td>1996</td>
      <td>0.203</td>
    </tr>
    <tr>
      <th>87</th>
      <td>1997</td>
      <td>0.112</td>
    </tr>
    <tr>
      <th>88</th>
      <td>1998</td>
      <td>0.206</td>
    </tr>
    <tr>
      <th>89</th>
      <td>1999</td>
      <td>0.153</td>
    </tr>
    <tr>
      <th>90</th>
      <td>2000</td>
      <td>0.092</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Number of disaster incidents by year
d_incidents = disasterinci.parse("FEMA Declarations")
dis_inci = d_incidents[["Year", "Incident Type"]]
incidents_count = dis_inci.groupby(["Year"]).count()
incidents_count.reset_index(level=0, inplace=True)
incidents_count = incidents_count.rename(columns = {"Incident Type":'Number of Disasters Incidents'})
incidents_count.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Number of Disasters Incidents</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1953</td>
      <td>13</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1954</td>
      <td>17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1955</td>
      <td>18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1956</td>
      <td>16</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1957</td>
      <td>16</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Temperature data: annual average temperatures in the contiguous 48 states have changed since 1901
bins = [1890, 1900, 1910, 1920, 1930, 1940, 1950, 1960, 1970, 1980, 1990, 2000, 2010, 2020]
decades = [1900,1910,1920, 1930, 1940, 1950, 1960, 1970, 1980, 1990, 2000, 2010, 2020]
temp_data["Decade"] = pd.cut(temp_data["Year"], bins, labels=decades)
temp_data = temp_data[["Year", "Earth's surface"]]
temp_data = temp_data.rename(columns = {"Earth's surface":'Temperature anomaly'})
temp_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Temperature anomaly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1901</td>
      <td>-0.15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1902</td>
      <td>-0.43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1903</td>
      <td>-1.40</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1904</td>
      <td>-0.86</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1905</td>
      <td>-1.02</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Number of disaster declarations per year
fema_declarations = disasterdec.parse("FEMA Declarations")
fema_declarations['year'] = fema_declarations['Declaration Date'].dt.year
declartion_year = fema_declarations.groupby(["year"]).count()
declartion_year.reset_index(level=0, inplace=True)
disaster_declaration = declartion_year[["year", "Incident Type"]]
disaster_declaration = disaster_declaration.rename(columns = {"Incident Type":'Number of Disaster Declarations'})
disaster_declaration.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>Number of Disaster Declarations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1953</td>
      <td>13</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1954</td>
      <td>17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1955</td>
      <td>18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1956</td>
      <td>16</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1957</td>
      <td>16</td>
    </tr>
  </tbody>
</table>
</div>




```python
incident_types = fema_declarations["Incident Type"].value_counts()
incident_types
```




    Severe Storm(s)     15963
    Hurricane            9727
    Flood                9496
    Snow                 3620
    Fire                 2656
    Severe Ice Storm     1990
    Tornado              1430
    Drought              1292
    Coastal Storm         462
    Freezing              301
    Other                 297
    Typhoon               119
    Earthquake            105
    Volcano                50
    Fishing Losses         42
    Mud/Landslide          10
    Toxic Substances        9
    Tsunami                 9
    Chemical                9
    Dam/Levee Break         6
    Human Cause             6
    Terrorist               5
    Name: Incident Type, dtype: int64




```python
incident_types = pd.DataFrame(incident_types)
incident_types = incident_types.rename(columns = {"" : "Disaster Type", "Incident Type": "Number of Disasters"})
incident_types.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Disasters</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Severe Storm(s)</th>
      <td>15963</td>
    </tr>
    <tr>
      <th>Hurricane</th>
      <td>9727</td>
    </tr>
    <tr>
      <th>Flood</th>
      <td>9496</td>
    </tr>
    <tr>
      <th>Snow</th>
      <td>3620</td>
    </tr>
    <tr>
      <th>Fire</th>
      <td>2656</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Number of Total Disasters Declarations (sum of all disasters) VS year 
#FEMA disaster declarations VS Year (two line graphs on top of each other)

sns.set()

# Create scatterplot of dataframe
sns.lmplot("year", "Number of Disaster Declarations", aspect = 1.5, data= disaster_declaration, fit_reg=True) 

# Set title and labels
plt.title('Number of Disaster Declarations vs. Year')
plt.xlabel('Year')
plt.ylabel('Number of Disaster Declarations')

#Save as png and show graph
DisasterDeclarations = plt.gcf()
plt.savefig('DisasterDeclarations.png')
plt.show()

```


![png](output_13_0.png)



```python
#FEMA budget VS Year | bar graph
plt.rcParams["figure.figsize"] = [9,5]
year_x = fema_funding['Year']
budget_g = fema_funding[['Administration Request', 'Enacted Appropriation', 'Emergency Supplemental ']].plot(x= year_x, kind='bar', stacked=True)
#budget_g.legend(loc='center left', bbox_to_anchor=(1, 0.5))

# Set title and labels
plt.title('FEMA Funding in billions of Real 2010 Dollars')
plt.xlabel('Year')
plt.ylabel('Dollars in Billions')

#Save as png and show graph
femafunding = plt.gcf()
plt.savefig('FEMAFunding.png')
plt.show()
```


![png](output_14_0.png)



```python
#Enacted appropriation + Administration request
femabudget = fema_funding['Administration Request'].tolist()

#Total paid on closed claims NFIP
paid_claims = nfip[(nfip_df["Year"] >= 2000) & (nfip["Year"] <= 2010)]
claims_budget = paid_claims['Total Paid on Closed Claims'].tolist()

#Enacted appropriation + Administration request AND Total paid on closed claims VS Year (2 line graph)
years = [2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010]

# Plot our line that will be used to track a wrestler's wins over the years
plt.plot(years, femabudget, color="green", label="Enacted Budget")

# Plot our line that will be used to track a wrestler's losses over the years
plt.plot(years, claims_budget, color="red", label="Total Paid on Closed Claims")

# Place a legend on the chart in what matplotlib believes to be the "best" location
plt.legend(loc="best")

plt.title("Enacted FEMA Budget Vs. Total Paid on NFIP Closed Claims")
plt.xlabel("Years")
plt.ylabel("Dollars in Billions")

# Print and save chart to the screen
Comparebudget = plt.gcf()
plt.savefig('CompareBudgetofFema.png')
plt.show()

```


![png](output_15_0.png)



```python
#Extreme one-day precipitation events from 1910 to 2015.

plt.plot(precip_df["Year"],precip_df["Index value"])
plt.title("Extreme one-day Precipitation Events from 1910 to 2015")
plt.xlabel("Years")
plt.ylabel("Percent of Land Area")
plt.savefig("Extreme one-day Precipitation Events from 1910 to 2015")
plt.show()
```


![png](output_16_0.png)



```python
#Annual Average Temperatures 1901-2015
plt.plot(temp_data["Year"], temp_data["Temperature anomaly"])
plt.title("Trends in Annual Average Temperatures 1901-2015")
plt.xlabel("Years")
plt.ylabel("Temperature Anomaly (Fahrenheit)")
plt.savefig("Trends in Annual Average Temperatures 1901-2015")
plt.show()
```


![png](output_17_0.png)



```python
# NUmber of Claims closed for National flood insurance Plan VS Year | bar graph
plt.rcParams["figure.figsize"] = [10,5]
sns.lmplot("Year", "Number of Claims Closed", data = nfip, aspect =2, fit_reg=True)
plt.title("Claims Closed With Payment for National Flood Insurance Plan")
plt.xlabel("Years")
plt.ylabel("Number of Claims Closed")
plt.savefig("Number of Claims Closed")
plt.show()
```


![png](output_18_0.png)



```python
#Trend in Average temperatures from 1901-2015 bar graph.
plt.figure(figsize=(20,10))
plt.rc('axes', titlesize = 16)
plt.rc('axes', labelsize =16)
plt.rc('xtick', labelsize = 15)
plt.title("Trends in Average Temperature 1901-2015")
plt.xlabel("Year")
plt.ylabel("Temperature Anomaly Fahrenheit")
plt.bar(temp_data["Year"],temp_data["Temperature anomaly"])
plt.savefig("Trends in Average Temperature 1901-2015-Bar Graph ")
plt.show()
```


![png](output_19_0.png)



```python
#Number of disasters type VS Disaster types (Bar Graph), 1953-2015.
incident_types.plot(kind="bar", facecolor="blue", figsize=(18,9), legend = False)
plt.rc('axes', titlesize = 16)
plt.rc('xtick', labelsize = 15)
plt.title("Number of Disaster Incidents by Type 1953 to 2015")
plt.xlabel("Disaster Type")
plt.ylabel("Number of Incidents")
plt.tight_layout()
plt.savefig("FEMA Disaster Declarations 1953-2015")
plt.show()
```


![png](output_20_0.png)

