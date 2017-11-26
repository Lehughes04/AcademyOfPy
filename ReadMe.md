
# Observations
1.	Charter schools on average had higher overall scores than that of district schools
2.	Smaller schools (less than 2,000 students) had higher average scores in reading and math.
3.	More spending per student did not necessarily result in better scores.  On average schools spending less 
than $625 per student had higher overall scores.



```python
import pandas as pd
import numpy as np
```


```python
#Create District Summary
file1 = '../10-16-2017-GW-Arlington-Class-Repository-DATA/Homework/04-Numpy-Pandas/Instructions/PyCitySchools/raw_data/schools_complete.csv'
file2 = '../10-16-2017-GW-Arlington-Class-Repository-DATA/Homework/04-Numpy-Pandas/Instructions/PyCitySchools/raw_data/students_complete.csv'
df_sch = pd.read_csv(file1,low_memory=False)
df_stu = pd.read_csv(file2, low_memory = False)
df_sch = df_sch.rename(columns={'name':'school'})
df_sch['budg_stu'] = df_sch['budget']/df_sch['size']
df_sch.head()
df_stu.head()
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
      <th>Student ID</th>
      <th>name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Victor Smith</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>94</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Kevin Rodriguez</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>90</td>
      <td>60</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Dr. Richard Scott</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>67</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Bonnie Ray</td>
      <td>F</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>97</td>
      <td>84</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Key metrics
#School count
school = df_sch["school"].count()
#Total Students
students = df_sch["size"].sum()
#Total Budget
budget = df_sch["budget"].sum()
#Average Math Scoore
avg_math = df_stu["math_score"].mean()
#Average Reading Score
avg_read = df_stu['reading_score'].mean()
#% passing math
per_math = df_stu.loc[df_stu['math_score']>=60]
pass_math = per_math['math_score'].count()
pm = pass_math/students*100
#% passing reading
per_read = df_stu.loc[df_stu['reading_score']>=60]
pass_read = per_read['reading_score'].count()
pr = pass_read/students*100
#Overall Passing Rate
poverall = (pm+pr)/2
```


```python
#District Summary
df_summary = pd.DataFrame({'school_count':[school],
                           'student_count':[students],
                           'total_budget':[budget],
                           'avg_math_score':[avg_math],
                           'avg_reading_score':[avg_read],
                           'percent_passing_math':[pm],
                           'percent_passing_reading':[pr],
                           'percent_passing_overall':[poverall]
                          })
df_summary.round(2)
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
      <th>avg_math_score</th>
      <th>avg_reading_score</th>
      <th>percent_passing_math</th>
      <th>percent_passing_overall</th>
      <th>percent_passing_reading</th>
      <th>school_count</th>
      <th>student_count</th>
      <th>total_budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>78.99</td>
      <td>81.88</td>
      <td>92.45</td>
      <td>96.22</td>
      <td>100.0</td>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
    </tr>
  </tbody>
</table>
</div>




```python
#per school summary - narrow scope of dataframe
df_stu1 = df_stu[['school','reading_score','math_score']]

#Use counts to get number of passing
df_stu1['reading_pass_count'] = [1 if x >=60 else 0 for x in df_stu1['reading_score']]
df_stu1['math_pass_count'] = [1 if x >=60 else 0 for x in df_stu1['math_score']]

#Group by school to get totals and averages
df_stu1 = df_stu1.groupby('school')
stu_pass = df_stu1['reading_pass_count','math_pass_count'].sum()
stu_pass = stu_pass.reset_index()
stu_avg = df_stu1['reading_score','math_score'].mean()
stu_avg = stu_avg.reset_index()

#merge dataframes to get data into 1 table
df_merge = pd.merge(stu_pass, stu_avg, on='school', how='outer')

#rename columns
df_merge = df_merge.rename(columns={'reading_score':'avg_reading_score','math_score':'avg_math_score'})
df_merge
```

    C:\Users\menta\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    C:\Users\menta\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:6: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    




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
      <th>school</th>
      <th>reading_pass_count</th>
      <th>math_pass_count</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>4976</td>
      <td>4455</td>
      <td>81.033963</td>
      <td>77.048432</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>1858</td>
      <td>1858</td>
      <td>83.975780</td>
      <td>83.061895</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>2949</td>
      <td>2608</td>
      <td>81.158020</td>
      <td>76.711767</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>2739</td>
      <td>2446</td>
      <td>80.746258</td>
      <td>77.102592</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>1468</td>
      <td>1468</td>
      <td>83.816757</td>
      <td>83.351499</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>4635</td>
      <td>4129</td>
      <td>80.934412</td>
      <td>77.289752</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>427</td>
      <td>427</td>
      <td>83.814988</td>
      <td>83.803279</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>2917</td>
      <td>2592</td>
      <td>81.182722</td>
      <td>76.629414</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>4761</td>
      <td>4246</td>
      <td>80.966394</td>
      <td>77.072464</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>962</td>
      <td>962</td>
      <td>84.044699</td>
      <td>83.839917</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>3999</td>
      <td>3541</td>
      <td>80.744686</td>
      <td>76.842711</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>1761</td>
      <td>1761</td>
      <td>83.725724</td>
      <td>83.359455</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>1635</td>
      <td>1635</td>
      <td>83.848930</td>
      <td>83.418349</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>2283</td>
      <td>2283</td>
      <td>83.989488</td>
      <td>83.274201</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>1800</td>
      <td>1800</td>
      <td>83.955000</td>
      <td>83.682222</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_merge1 = pd.merge(df_merge,df_sch, on='school', how='outer')

#Get percentages passing
df_merge1['reading_pass_per'] = (df_merge1['reading_pass_count']/df_merge1['size'])*100
df_merge1['math_pass_per'] = (df_merge1['math_pass_count']/df_merge1['size'])*100
df_merge1.head()

#Summary School Table
df_summary = df_merge1.iloc[:,[0,6,7,8,9,3,4,10,11]]
df_summary['overall_pass_per'] = (df_summary['reading_pass_per']+df_summary['math_pass_per'])/2
df_summary = df_summary.rename(columns={'school':'school_name','size':'total_students','budget':'total_budget',
                                       'budg_stu':'budget_per_student'
                                       }
                              )
df_summary
```

    C:\Users\menta\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    




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
      <th>school_name</th>
      <th>type</th>
      <th>total_students</th>
      <th>total_budget</th>
      <th>budget_per_student</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>81.033963</td>
      <td>77.048432</td>
      <td>100.0</td>
      <td>89.529743</td>
      <td>94.764871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>100.0</td>
      <td>88.436758</td>
      <td>94.218379</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>100.0</td>
      <td>89.302665</td>
      <td>94.651333</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>100.0</td>
      <td>89.083064</td>
      <td>94.541532</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>100.0</td>
      <td>88.858416</td>
      <td>94.429208</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>100.0</td>
      <td>89.182945</td>
      <td>94.591472</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>100.0</td>
      <td>88.547137</td>
      <td>94.273568</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.848930</td>
      <td>83.418349</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top performing schools by overall passing rate
df_summary.sort_values(by=['overall_pass_per'], ascending = False, inplace = True)
df_summary.head(5)

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
      <th>school_name</th>
      <th>type</th>
      <th>total_students</th>
      <th>total_budget</th>
      <th>budget_per_student</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Bottom performing schools by overall passing rate
df_summary.sort_values(by=['overall_pass_per'], ascending = True, inplace = True)
df_summary.head(5)
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
      <th>school_name</th>
      <th>type</th>
      <th>total_students</th>
      <th>total_budget</th>
      <th>budget_per_student</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>100.0</td>
      <td>88.436758</td>
      <td>94.218379</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>100.0</td>
      <td>88.547137</td>
      <td>94.273568</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>100.0</td>
      <td>88.858416</td>
      <td>94.429208</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>100.0</td>
      <td>89.083064</td>
      <td>94.541532</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>100.0</td>
      <td>89.182945</td>
      <td>94.591472</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Reading scores by grade by school

df_stugrp = df_stu[['school','grade','reading_score','math_score']]

#group by school, grade
df_stugrp = df_stugrp.groupby(['school','grade'])

#Average score
df_stugrp1 = pd.DataFrame(df_stugrp['reading_score'].mean())
df_stugrp1 = df_stugrp1.reset_index()

#Rename column
df_stugrp1 = df_stugrp1.rename(columns = {'reading_score':'avg_reading_score'})

#Sort data
df_stugrp1.sort_values(by=['school','grade'],ascending=['school','grade'])
df_stugrp1
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
      <th>school</th>
      <th>grade</th>
      <th>avg_reading_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>10th</td>
      <td>80.907183</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bailey High School</td>
      <td>11th</td>
      <td>80.945643</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bailey High School</td>
      <td>12th</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bailey High School</td>
      <td>9th</td>
      <td>81.303155</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cabrera High School</td>
      <td>10th</td>
      <td>84.253219</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Cabrera High School</td>
      <td>11th</td>
      <td>83.788382</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>12th</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Cabrera High School</td>
      <td>9th</td>
      <td>83.676136</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Figueroa High School</td>
      <td>10th</td>
      <td>81.408912</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Figueroa High School</td>
      <td>11th</td>
      <td>80.640339</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Figueroa High School</td>
      <td>12th</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Figueroa High School</td>
      <td>9th</td>
      <td>81.198598</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Ford High School</td>
      <td>10th</td>
      <td>81.262712</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>11th</td>
      <td>80.403642</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Ford High School</td>
      <td>12th</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Ford High School</td>
      <td>9th</td>
      <td>80.632653</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Griffin High School</td>
      <td>10th</td>
      <td>83.706897</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Griffin High School</td>
      <td>11th</td>
      <td>84.288089</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Griffin High School</td>
      <td>12th</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Griffin High School</td>
      <td>9th</td>
      <td>83.369193</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Hernandez High School</td>
      <td>10th</td>
      <td>80.660147</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Hernandez High School</td>
      <td>11th</td>
      <td>81.396140</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Hernandez High School</td>
      <td>12th</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Hernandez High School</td>
      <td>9th</td>
      <td>80.866860</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Holden High School</td>
      <td>10th</td>
      <td>83.324561</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Holden High School</td>
      <td>11th</td>
      <td>83.815534</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Holden High School</td>
      <td>12th</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Holden High School</td>
      <td>9th</td>
      <td>83.677165</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Huang High School</td>
      <td>10th</td>
      <td>81.512386</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Huang High School</td>
      <td>11th</td>
      <td>81.417476</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Huang High School</td>
      <td>12th</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Huang High School</td>
      <td>9th</td>
      <td>81.290284</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Johnson High School</td>
      <td>10th</td>
      <td>80.773431</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Johnson High School</td>
      <td>11th</td>
      <td>80.616027</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Johnson High School</td>
      <td>12th</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Johnson High School</td>
      <td>9th</td>
      <td>81.260714</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Pena High School</td>
      <td>10th</td>
      <td>83.612000</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pena High School</td>
      <td>11th</td>
      <td>84.335938</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pena High School</td>
      <td>12th</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Pena High School</td>
      <td>9th</td>
      <td>83.807273</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Rodriguez High School</td>
      <td>10th</td>
      <td>80.629808</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Rodriguez High School</td>
      <td>11th</td>
      <td>80.864811</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Rodriguez High School</td>
      <td>12th</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Rodriguez High School</td>
      <td>9th</td>
      <td>80.993127</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Shelton High School</td>
      <td>10th</td>
      <td>83.441964</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Shelton High School</td>
      <td>11th</td>
      <td>84.373786</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Shelton High School</td>
      <td>12th</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Shelton High School</td>
      <td>9th</td>
      <td>84.122642</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Thomas High School</td>
      <td>10th</td>
      <td>84.254157</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Thomas High School</td>
      <td>11th</td>
      <td>83.585542</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Thomas High School</td>
      <td>12th</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Thomas High School</td>
      <td>9th</td>
      <td>83.728850</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Wilson High School</td>
      <td>10th</td>
      <td>84.021452</td>
    </tr>
    <tr>
      <th>53</th>
      <td>Wilson High School</td>
      <td>11th</td>
      <td>83.764608</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Wilson High School</td>
      <td>12th</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>55</th>
      <td>Wilson High School</td>
      <td>9th</td>
      <td>83.939778</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Wright High School</td>
      <td>10th</td>
      <td>83.812757</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Wright High School</td>
      <td>11th</td>
      <td>84.156322</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Wright High School</td>
      <td>12th</td>
      <td>84.073171</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Wright High School</td>
      <td>9th</td>
      <td>83.833333</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Math scores by grade by school

#Average score
df_stugrp2 = pd.DataFrame(df_stugrp['math_score'].mean())
df_stugrp2 = df_stugrp2.reset_index()

#Rename column
df_stugrp2 = df_stugrp2.rename(columns = {'math_score':'avg_math_score'})

#Sort data
df_stugrp2.sort_values(by=['school','grade'],ascending=['school','grade'])
df_stugrp2
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
      <th>school</th>
      <th>grade</th>
      <th>avg_math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>10th</td>
      <td>76.996772</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bailey High School</td>
      <td>11th</td>
      <td>77.515588</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bailey High School</td>
      <td>12th</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bailey High School</td>
      <td>9th</td>
      <td>77.083676</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cabrera High School</td>
      <td>10th</td>
      <td>83.154506</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Cabrera High School</td>
      <td>11th</td>
      <td>82.765560</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>12th</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Cabrera High School</td>
      <td>9th</td>
      <td>83.094697</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Figueroa High School</td>
      <td>10th</td>
      <td>76.539974</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Figueroa High School</td>
      <td>11th</td>
      <td>76.884344</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Figueroa High School</td>
      <td>12th</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Figueroa High School</td>
      <td>9th</td>
      <td>76.403037</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Ford High School</td>
      <td>10th</td>
      <td>77.672316</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>11th</td>
      <td>76.918058</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Ford High School</td>
      <td>12th</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Ford High School</td>
      <td>9th</td>
      <td>77.361345</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Griffin High School</td>
      <td>10th</td>
      <td>84.229064</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Griffin High School</td>
      <td>11th</td>
      <td>83.842105</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Griffin High School</td>
      <td>12th</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Griffin High School</td>
      <td>9th</td>
      <td>82.044010</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Hernandez High School</td>
      <td>10th</td>
      <td>77.337408</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Hernandez High School</td>
      <td>11th</td>
      <td>77.136029</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Hernandez High School</td>
      <td>12th</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Hernandez High School</td>
      <td>9th</td>
      <td>77.438495</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Holden High School</td>
      <td>10th</td>
      <td>83.429825</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Holden High School</td>
      <td>11th</td>
      <td>85.000000</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Holden High School</td>
      <td>12th</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Holden High School</td>
      <td>9th</td>
      <td>83.787402</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Huang High School</td>
      <td>10th</td>
      <td>75.908735</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Huang High School</td>
      <td>11th</td>
      <td>76.446602</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Huang High School</td>
      <td>12th</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Huang High School</td>
      <td>9th</td>
      <td>77.027251</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Johnson High School</td>
      <td>10th</td>
      <td>76.691117</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Johnson High School</td>
      <td>11th</td>
      <td>77.491653</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Johnson High School</td>
      <td>12th</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Johnson High School</td>
      <td>9th</td>
      <td>77.187857</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Pena High School</td>
      <td>10th</td>
      <td>83.372000</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pena High School</td>
      <td>11th</td>
      <td>84.328125</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Pena High School</td>
      <td>12th</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Pena High School</td>
      <td>9th</td>
      <td>83.625455</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Rodriguez High School</td>
      <td>10th</td>
      <td>76.612500</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Rodriguez High School</td>
      <td>11th</td>
      <td>76.395626</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Rodriguez High School</td>
      <td>12th</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Rodriguez High School</td>
      <td>9th</td>
      <td>76.859966</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Shelton High School</td>
      <td>10th</td>
      <td>82.917411</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Shelton High School</td>
      <td>11th</td>
      <td>83.383495</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Shelton High School</td>
      <td>12th</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Shelton High School</td>
      <td>9th</td>
      <td>83.420755</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Thomas High School</td>
      <td>10th</td>
      <td>83.087886</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Thomas High School</td>
      <td>11th</td>
      <td>83.498795</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Thomas High School</td>
      <td>12th</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Thomas High School</td>
      <td>9th</td>
      <td>83.590022</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Wilson High School</td>
      <td>10th</td>
      <td>83.724422</td>
    </tr>
    <tr>
      <th>53</th>
      <td>Wilson High School</td>
      <td>11th</td>
      <td>83.195326</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Wilson High School</td>
      <td>12th</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>55</th>
      <td>Wilson High School</td>
      <td>9th</td>
      <td>83.085578</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Wright High School</td>
      <td>10th</td>
      <td>84.010288</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Wright High School</td>
      <td>11th</td>
      <td>83.836782</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Wright High School</td>
      <td>12th</td>
      <td>83.644986</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Wright High School</td>
      <td>9th</td>
      <td>83.264706</td>
    </tr>
  </tbody>
</table>
</div>




```python
#breakdown School performance by Spending Ranges per Student
df_summary
bins = [575,600,625,650,675]
groupnames = ['>575_<600','>600_<625','>625_<650','>650_<675']

#binning the summary table
df_summarybin = df_summary[['budget_per_student','avg_reading_score','avg_math_score',
                           'reading_pass_per','math_pass_per','overall_pass_per']]
df_summarybin['spending_range'] = pd.cut(df_summary['budget_per_student'],bins,labels = groupnames)

#Group by Spending Range
spending_range = df_summarybin.groupby(['spending_range'])
spending_range.mean()
```

    C:\Users\menta\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:9: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    




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
      <th>budget_per_student</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
    <tr>
      <th>spending_range</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&gt;575_&lt;600</th>
      <td>584.800000</td>
      <td>83.892196</td>
      <td>83.436210</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>&gt;600_&lt;625</th>
      <td>617.000000</td>
      <td>83.930728</td>
      <td>83.595708</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>&gt;625_&lt;650</th>
      <td>639.333333</td>
      <td>81.416375</td>
      <td>78.032719</td>
      <td>100.0</td>
      <td>90.833208</td>
      <td>95.416604</td>
    </tr>
    <tr>
      <th>&gt;650_&lt;675</th>
      <td>653.500000</td>
      <td>81.058567</td>
      <td>76.959583</td>
      <td>100.0</td>
      <td>88.970740</td>
      <td>94.485370</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Summary table for School size
#breakdown School performance by School Size
bins = [0,2000,4000,6000]
groupnames = ['small','medium','large']

#binning the summary table
df_summarybin2 = df_summary[['total_students','avg_reading_score','avg_math_score',
                           'reading_pass_per','math_pass_per','overall_pass_per']]
df_summarybin2['school_size'] = pd.cut(df_summary['total_students'],bins,labels = groupnames)

#Group by School Size
school_size = df_summarybin2.groupby(['school_size'])
school_size.mean()
```

    C:\Users\menta\Anaconda3\envs\PythonData\lib\site-packages\ipykernel\__main__.py:9: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    




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
      <th>total_students</th>
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
    <tr>
      <th>school_size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>large</th>
      <td>4790.666667</td>
      <td>80.978256</td>
      <td>77.136883</td>
      <td>100.0</td>
      <td>89.265250</td>
      <td>94.632625</td>
    </tr>
    <tr>
      <th>medium</th>
      <td>2977.400000</td>
      <td>81.564235</td>
      <td>78.112137</td>
      <td>100.0</td>
      <td>91.028995</td>
      <td>95.514498</td>
    </tr>
    <tr>
      <th>small</th>
      <td>1415.857143</td>
      <td>83.883125</td>
      <td>83.502373</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Summary table by Type
df_summarybin3 = df_summary[['type','avg_reading_score','avg_math_score',
                           'reading_pass_per','math_pass_per','overall_pass_per']]

#Group by School Type
school_type = df_summarybin3.groupby(['type'])
school_type.mean()
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
      <th>avg_reading_score</th>
      <th>avg_math_score</th>
      <th>reading_pass_per</th>
      <th>math_pass_per</th>
      <th>overall_pass_per</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.896421</td>
      <td>83.473852</td>
      <td>100.0</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>District</th>
      <td>80.966636</td>
      <td>76.956733</td>
      <td>100.0</td>
      <td>88.991533</td>
      <td>94.495766</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
