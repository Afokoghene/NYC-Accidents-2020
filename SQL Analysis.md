## Solutions to all preset questions

1. Compare the % of total accidents by month. Do you notice any seasonal patterns?
```sql
SELECT DATENAME (month, DateOfCrash) MonthOfCrash, 
	   ROUND (COUNT(*) * 100.0 / SUM (COUNT(*)) OVER(), 2) AS MonthlyPercentage
FROM SQLPractice.dbo.NYCAccidents2020
GROUP BY DATENAME (month, DateOfCrash)
ORDER BY MonthlyPercentage DESC
```
| MonthOfCrash | MonthlyPercentage |
|------------------|-------------|
|    January    |   19.08   |
|    February    |   18.27   |
|    March    |   14.77   |
|    July    |   12.32    |
|    August    |  11.68    |
|    June    |   10.17   |
|    May    |   8.21   |
|    April    |   5.50   |


2. Break down accident frequency by day of week and hour of day. Based on this data, when do accidents occur most frequently?
- Weekday with most accident occurrence
```sql
SELECT DATENAME (weekday, DateOfCrash) DayOfCrash, COUNT (CollisionId) CollisionIdCount 
FROM SQLPractice.dbo.NYCAccidents2020
GROUP BY DATENAME (weekday, DateOfCrash)
ORDER BY COUNT (CollisionId) DESC
```
| DayOfCrash | CollisionIdCount |
|------------------|-------------|
|    Friday    |   12271  |
|    Thursday    |   11244   |
|    Wednesday    |   10638   |
|    Tuesday    |   10613   |
|    Saturday    |  10601    |
|    Monday    |   10511   |
|    Sunday    |   9003  |

- Hour of the day with most accident occurrence
```sql
SELECT CONVERT (varchar(5), DATEPART (hour, TimeOfCrash)) + ':00' AS HourOfCrash,
       COUNT (CollisionId) AS CollisionIdCount
FROM SQLPractice.dbo.NYCAccidents2020
GROUP BY DATEPART (hour, TimeOfCrash)
ORDER BY CollisionIdCount DESC
```
| HourOfCrash | CollisionIdCount |
|------------------|-------------|
|    16:00    |   5219  |
|    14:00    |   5016   |
|    17:00    |   4974   |
|    18:00    |   4696   |
|    15:00    |  4677    |
|    13:00    |   4458   |
|    12:00    |   4054  |
|    11:00    |   3803  |
|    19:00    |   3738  |
|    08:00    |   3678  |
|    10:00    |   3525  |
|    09:00    |   3439  |
|    20:00    |   3138  |
|    00:00    |   2948  |
|    21:00    |   2711  |
|    22:00    |   2557  |
|    07:00    |   2463  |
|    23:00    |   2164  |
|    06:00    |   1868  |
|    01:00    |   1474  |
|    05:00    |   1178  |
|    02:00    |   1139  |
|    03:00    |   989  |
|    04:00    |   975  |


3. On which particular street were the most accidents reported? What does that represent as a % of all reported accidents?
- This query will select all including the streets recorded as **NULL** and also limit to the top 7
```sql
SELECT TOP 7 OnStreetName, 
	   ROUND (COUNT(*) * 100.0 / SUM (COUNT(*)) OVER(), 2) OnStreetNamePercentage
FROM SQLPractice.dbo.NYCAccidents2020
GROUP BY OnStreetName
ORDER BY OnStreetNamePercentage DESC
```
| OnStreetName | OnStreetNamePercentage |
|------------------|-------------|
|    NULL    |   25.96  |
|    BELT PARKWAY    |   1.66   |
|    BROOKLYN QUEENS EXPRESSWAY    |   0.99   |
|    LONG ISLAND EXPRESSWAY    |   0.99   |
|    FDR DRIVE   |  0.97    |
|    MAJOR DEEGAN EXPRESSWAY   |   0.79   |
|    GRAND CENTRAL PARKWAY   |   0.78  |

- This query will select all excluding the streets recorded as **NULL** and also limit to the top 6
```sql
SELECT TOP 6 OnStreetName, 
	   ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) OnStreetNamePercentage
FROM SQLPractice.dbo.NYCAccidents2020
WHERE OnStreetName IS NOT NULL
GROUP BY OnStreetName
ORDER BY OnStreetNamePercentage DESC
```
| OnStreetName | OnStreetNamePercentage |
|------------------|-------------|
|    BELT PARKWAY    |   2.25   |
|    BROOKLYN QUEENS EXPRESSWAY    |   1.34  |
|    LONG ISLAND EXPRESSWAY    |   1.33  |
|    FDR DRIVE   |  1.31    |
|    MAJOR DEEGAN EXPRESSWAY   |   1.07   |
|    GRAND CENTRAL PARKWAY   |   1.05  |


4. What was the most common contributing factor for the accidents reported in this sample (based on Vehicle 1)? What about fatal accidents specifically?
   _Unspecified_ in the result is recorded just as it is and not returned as _NULL_. It is possible that the cause of those accidents were not specififed when 
   reported.
   
- This query will return the 10 most contributing factor to accidents as reported in the sample. 
```sql
SELECT TOP 10 ContributingFactorVehicle1, COUNT (CollisionId) CountCollisionId
FROM SQLPractice.dbo.NYCAccidents2020
GROUP BY ContributingFactorVehicle1
ORDER BY CountCollisionId DESC
```
| ContributingFactorVehicle1 | CountCollisionId |
|------------------|-------------|
|    Unspecified    |   19471  |
|    Driver Inattention/Distraction    |   19123  |
|    Following Too Closely    |   5202   |
|    Failure to Yield Right-of-Way   |   4815  |
|    Passing or Lane Usage Improper   |  2840    |
|    Backing Unsafely   |   2829   |
|    Passing Too Closely   |   2687  |
|    Other Vehicular   |   2233  |
|    Unsafe Speed   |   2015  |
|    Unsafe Lane Changing   |   1809  |

- This query will return the 6 most contributing factor to accidents that had fatalities(led to deaths).
```sql
SELECT TOP 6 ContributingFactorVehicle1, COUNT(CollisionId) CountCollisionId
FROM SQLPractice.dbo.NYCAccidents2020
WHERE NumberOfPersonsKilled > 0
GROUP BY ContributingFactorVehicle1
ORDER BY CountCollisionId DESC
```
| ContributingFactorVehicle1 | CountCollisionId |
|------------------|-------------|
|    Unsafe Speed    |   33  |
|    Unspecified    |   32  |
|    Traffic Control Disregarded   |   15   |
|    Driver Inattention/Distraction   |   13  |
|    Failure to Yield Right-of-Way   |  10    |
|    Pedestrian/Bicyclist/Other Pedestrian Error/Confusion   |   6   |
