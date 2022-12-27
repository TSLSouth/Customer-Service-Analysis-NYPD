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
3. EDA;
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

```python
df.shape
```

```python
df.info()
```

```python
plt.figure(figsize=(10,10))
sns.scatterplot(data=df, x='Longitude', y='Latitude', hue=df.value_counts(), alpha=0.2)
```

---

Aproximated map of NYPD Precincts:

![img nypd precincts](https://www.researchgate.net/profile/Eloise-Dunlap/publication/247923137/figure/fig1/AS:202655319236622@1425328201555/figure-fig1.png)


---

Complaint types:

```python
plt.figure(figsize=(18,6))
df['Complaint Type'].value_counts().plot(kind='bar')
```

```python
plt.figure(figsize=(18,6))
df['Descriptor'].value_counts().head(10).plot(kind='barh', grid=True)
```

---
Quantity of complaints by borough:

```python
plt.figure(figsize=(10,4))
df['Borough'].value_counts().plot(kind='bar')
```

```python
plt.figure(figsize=(18,6))
df['City'].value_counts().plot(kind='bar')
```

```python
df.value_counts(['Borough', 'Complaint Type']).head(10)
```

---
Biggest complaints in Brooklyn:

```python
df_brooklyn = df.loc[(df['Borough'] == 'BROOKLYN')]
```

```python
plt.figure(figsize=(10,4))
df_brooklyn['Complaint Type'].value_counts().head(10).plot(kind='bar')
```

**Observations:** Driveway questions are the primary 'problem' in Brooklyn followed by Noise.


---
Biggest complaints in Manhattan:

```python
df_manhattan = df.loc[(df['Borough'] == 'MANHATTAN')]

plt.figure(figsize=(10,4))
df_manhattan['Complaint Type'].value_counts().head(10).plot(kind='bar')
```

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

**Observations:** Just like Brooklyn, in Queens, driveway questions are the primary 'problem' followed by Noise.


---
Time to close a complaint:

```python
df['Created Date'] = pd.to_datetime(df['Created Date'])
```

```python
df['Closed Date'] = pd.to_datetime(df['Closed Date'])
```

```python
df['Open Case Time'] = df['Closed Date'] - df['Created Date']
```

```python
df_opencasetime = df['Open Case Time'].groupby(df['Borough']).mean().sort_values(ascending=False)
df_opencasetime
```

**Observations:** Bronx has 3x more time to resolve a case than Manhattan. Queens and Brooklyn, 2x more.

```python
df_opencasetime.astype('timedelta64[m]').plot(kind='bar')
```

---
Top 10 complaints that take the longest to resolve:

```python
df_opencasetime = df['Open Case Time'].groupby(df['Complaint Type']).mean().sort_values(ascending=False)
df_opencasetime.head(10)
```

```python
plt.figure(figsize=(10,4))
df_opencasetime.astype('timedelta64[m]').plot(kind='bar')
```

```python
plt.figure(figsize=(10,4))
df_opencasetime.drop('Animal in a Park').astype('timedelta64[m]').plot(kind='bar')
```

**Observations**:  There is signficant difference in mean of Open Case Time for different Complaints.


---
### **Recommedations:**

1) Analyze the service processes in the neighborhoods with the most requests (Brooklyn, Queens and Bronx) to decrease the time that cases remain open.
   
2) Since driveway blocking (and correlated issues) and noise constitute the largest number of requests, specialized teams could be directed. As done by the 34th Precinct that has been working to address noisy businesses along Dyckman Street since 2018, focusing on the hot spots with the most complaints and adding more officers to midnight patrol. [Source](https://nypost.com/2018/03/04/these-nyc-neighborhoods-have-the-most-noise-complaints/).
