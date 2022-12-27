# Project: Customer Service Analysis of NYPD

<br>

### **Abstract:**

We grab complaints by neighborhood that respond to NYPD as we seen in the maps below. Our goals is try to undertand what else the department could be doing to adress the problems faster and more assertively.

<br>

### **Relevant research on the subject matter:**

NYC Open Data offers access to every data we need.

<br>

### **Walk through:**

1. Issue Understanding;
2. Data Understanding;
3. EDA - Exploratory Data Analysis;
4. Recommendations.
   
<br>

### **Author:**

Luiz Furtado <br>
[LinkedIn](https://www.linkedin.com/in/luiz-furtado-dev/) | [GitHub](https://github.com/TSLSouth)

<br>

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

```python
df = pd.read_csv('311_Service_Requests_from_2010_to_Present.csv')
df.head()
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035311.png?raw=true)

```python
df.shape

(300698, 53)
```

```python
df.info()

RangeIndex: 300698 entries, 0 to 300697
Data columns (total 53 columns):
 #   Column                          Non-Null Count   Dtype  
---  ------                          --------------   -----  
 0   Unique Key                      300698 non-null  int64  
 1   Created Date                    300698 non-null  object 
 2   Closed Date                     298534 non-null  object 
 3   Agency                          300698 non-null  object 
 4   Agency Name                     300698 non-null  object 
 5   Complaint Type                  300698 non-null  object 
 6   Descriptor                      294784 non-null  object 
 7   Location Type                   300567 non-null  object 
 8   Incident Zip                    298083 non-null  float64
 9   Incident Address                256288 non-null  object 
 10  Street Name                     256288 non-null  object 
 11  Cross Street 1                  251419 non-null  object 
 12  Cross Street 2                  250919 non-null  object 
 13  Intersection Street 1           43858 non-null   object 
 14  Intersection Street 2           43362 non-null   object 
 15  Address Type                    297883 non-null  object 
 16  City                            298084 non-null  object 
 17  Landmark                        349 non-null     object 
 18  Facility Type                   298527 non-null  object 
 19  Status                          300698 non-null  object 
...
 51  Longitude                       297158 non-null  float64
 52  Location                        297158 non-null  object 
```

```python
plt.figure(figsize=(10,10))
sns.scatterplot(data=df, x='Longitude', y='Latitude', hue=df.value_counts(), alpha=0.2)
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_034835.png)

---

Aproximated map of NYPD Precincts:

![img nypd precincts](https://www.researchgate.net/profile/Eloise-Dunlap/publication/247923137/figure/fig1/AS:202655319236622@1425328201555/figure-fig1.png)


---

Complaint types:

```python
plt.figure(figsize=(18,6))
df['Complaint Type'].value_counts().plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_034935.png?raw=true)


```python
plt.figure(figsize=(18,6))
df['Descriptor'].value_counts().head(10).plot(kind='barh', grid=True)
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_034935.png?raw=true)

---
Quantity of complaints by borough:

```python
plt.figure(figsize=(10,4))
df['Borough'].value_counts().plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_034952.png?raw=true)

```python
plt.figure(figsize=(18,6))
df['City'].value_counts().plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035002.png?raw=true)


```python
df.value_counts(['Borough', 'Complaint Type']).head(10)

Borough    Complaint Type         
QUEENS     Blocked Driveway           31644
BROOKLYN   Blocked Driveway           28148
           Illegal Parking            27462
QUEENS     Illegal Parking            21982
MANHATTAN  Noise - Street/Sidewalk    20550
           Noise - Commercial         14560
BROOKLYN   Noise - Street/Sidewalk    13355
BRONX      Blocked Driveway           12755
MANHATTAN  Illegal Parking            12132
BROOKLYN   Noise - Commercial         11463
```

---
Biggest complaints in Brooklyn:

```python
df_brooklyn = df.loc[(df['Borough'] == 'BROOKLYN')]

plt.figure(figsize=(10,4))
df_brooklyn['Complaint Type'].value_counts().head(10).plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035739.png?raw=true)


**Observations:** Driveway questions are the primary 'problem' in Brooklyn followed by Noise.


---
Biggest complaints in Manhattan:

```python
df_manhattan = df.loc[(df['Borough'] == 'MANHATTAN')]

plt.figure(figsize=(10,4))
df_manhattan['Complaint Type'].value_counts().head(10).plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035747.png?raw=true)


**Observations:** Noise is the primary 'problem' in Manhattan and homeless encampment shows up as a problem.


---
Noise problem has been rising in NY: 
<br>
Source: [NYC Open Data](https://data.cityofnewyork.us/Social-Services/311-Noise-Complaints/p5f6-bkga)

![img noise graph](https://indowwindows.com/wp-content/uploads/2020/01/noise-complaints-nyc.png)


---
Biggest complaints in Queens:

```python
df_queens = df.loc[(df['Borough'] == 'QUEENS')]

plt.figure(figsize=(10,4))
df_queens['Complaint Type'].value_counts().head(10).plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035759.png?raw=true)


**Observations:** Just like Brooklyn, in Queens, driveway questions are the primary 'problem' followed by Noise.


---
Time to close a complaint:

```python
df['Created Date'] = pd.to_datetime(df['Created Date'])

df['Closed Date'] = pd.to_datetime(df['Closed Date'])

df['Open Case Time'] = df['Closed Date'] - df['Created Date']

df_opencasetime = df['Open Case Time'].groupby(df['Borough']).mean().sort_values(ascending=False)
df_opencasetime

Borough
Unspecified     0 days 10:23:34.302575107
BRONX           0 days 06:05:46.606187188
QUEENS          0 days 04:52:16.450111002
BROOKLYN        0 days 04:02:52.278498397
STATEN ISLAND   0 days 03:52:46.542632517
MANHATTAN       0 days 02:58:22.571767837
```

**Observations:** Bronx has 3x more time to resolve a case than Manhattan. Queens and Brooklyn, 2x more.

```python
df_opencasetime.astype('timedelta64[m]').plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035810.png?raw=true)

---
Top 10 complaints that take the longest to resolve:

```python
df_opencasetime = df['Open Case Time'].groupby(df['Complaint Type']).mean().sort_values(ascending=False)
df_opencasetime.head(10)

Complaint Type
Animal in a Park               14 days 00:50:05
Derelict Vehicle      0 days 07:21:50.902717762
Graffiti              0 days 07:09:04.504424778
Agency Issues         0 days 05:15:37.166666666
Animal Abuse          0 days 05:12:47.677780638
Blocked Driveway      0 days 04:44:27.258638198
Illegal Parking       0 days 04:30:04.145454301
Panhandling           0 days 04:22:21.963934426
Homeless Encampment   0 days 04:21:56.052536231
Squeegee                 0 days 04:02:44.250000
```

```python
plt.figure(figsize=(10,4))
df_opencasetime.astype('timedelta64[m]').plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035820.png?raw=true)


```python
plt.figure(figsize=(10,4))
df_opencasetime.drop('Animal in a Park').astype('timedelta64[m]').plot(kind='bar')
```

![img](https://github.com/TSLSouth/Customer-Service-Analysis-NYPD/blob/main/img/Screenshot_20221227_035827.png?raw=true)


**Observations**:  There is signficant difference in mean of Open Case Time for different Complaints.


---
### **Recommedations:**

1) Analyze the service processes in the neighborhoods with the most requests (Brooklyn, Queens and Bronx) to decrease the time that cases remain open.
   
2) Since driveway blocking (and correlated issues) and noise constitute the largest number of requests, specialized teams could be directed. As done by the 34th Precinct that has been working to address noisy businesses along Dyckman Street since 2018, focusing on the hot spots with the most complaints and adding more officers to midnight patrol. [Source](https://nypost.com/2018/03/04/these-nyc-neighborhoods-have-the-most-noise-complaints/).
