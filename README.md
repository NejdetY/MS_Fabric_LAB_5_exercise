# MS_Fabric_LAB_5_exercise
# Working with Data in Microsoft Fabric Eventhouse

## Overview
This project demonstrates how to create and query an Eventhouse in Microsoft Fabric using real-time taxi trip data.

## Getting Started

### 1. Create a Workspace
- Go to Microsoft Fabric Home and sign in.
- From the left menu, select **Workspaces**.
- Create a new workspace.
- Ensure Fabric capacity is enabled.

### 2. Create an Eventhouse
- Select **Workloads** > **Real-Time Intelligence**.
- Click **Explore Real-Time Intelligence Sample**.
- An Eventhouse named `RTISample` will be created automatically.
- A KQL database and a `Bikestream` table will also be created.

## Querying Data

### 3. Query with Kusto Query Language (KQL)

#### Take 100 rows
Bikestream
| take 100

#### Project specific columns
Bikestream
| project Street, No_Bikes
| take 10

#### Rename columns
Bikestream
| project Street, ["Number of Empty Docks"] = No_Empty_Docks
| take 10

#### Summarize total bikes
Bikestream
| summarize ["Total Number of Bikes"] = sum(No_Bikes)

#### Group by Neighbourhood
Bikestream
| summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
| project Neighbourhood, ["Total Number of Bikes"]

#### Handle empty or null neighbourhoods
Bikestream
| summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
| project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]

#### Sort ascending
Bikestream
| summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
| project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]
| order by Neighbourhood asc

#### Filter for Chelsea
Bikestream
| where Neighbourhood == "Chelsea"
| summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
| project Neighbourhood, ["Total Number of Bikes"]
| order by Neighbourhood asc

### 4. Query with Transact-SQL (T-SQL)

#### Take 100 rows
SELECT TOP 100 * 
FROM Bikestream

#### Project specific columns
SELECT TOP 10 Street, No_Bikes
FROM Bikestream

#### Rename columns
SELECT TOP 10 Street, No_Empty_Docks AS [Number of Empty Docks]
FROM Bikestream

#### Summarize total bikes
SELECT SUM(No_Bikes) AS [Total Number of Bikes]
FROM Bikestream

#### Group by Neighbourhood
SELECT Neighbourhood, SUM(No_Bikes) AS [Total Number of Bikes]
FROM Bikestream
GROUP BY Neighbourhood

#### Handle empty or null neighbourhoods
SELECT 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END AS Neighbourhood,
  SUM(No_Bikes) AS [Total Number of Bikes]
FROM Bikestream
GROUP BY 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END

#### Sort ascending
SELECT 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END AS Neighbourhood,
  SUM(No_Bikes) AS [Total Number of Bikes]
FROM Bikestream
GROUP BY 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END
ORDER BY Neighbourhood ASC

#### Filter for Chelsea
SELECT 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END AS Neighbourhood,
  SUM(No_Bikes) AS [Total Number of Bikes]
FROM Bikestream
GROUP BY 
  CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
  END
HAVING Neighbourhood = 'Chelsea'
ORDER BY Neighbourhood ASC

## Cleaning Up

- Go to Workspace Settings.
- Delete the workspace to clean up resources.

## Resources

- Microsoft Fabric Documentation: https://learn.microsoft.com/en-us/fabric/
- Kusto Query Language (KQL) Basics: https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/
