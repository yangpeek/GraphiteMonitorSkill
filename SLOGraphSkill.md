## SLO Graph Skill

### SLO based on Time Aggregation:
* MovingAverage based on the SLO definition is highly recommended for the sli monitoring

### Percentage Monitoring filter:
* Filter out the percentage which is beyond the expected ratio

### Auto Identify the tiered graph based on the %:
* assuming Node 1 is for the dc names: 
* assuming Node 2 is for the inventory item names: 

* Step 1: Idenitfy the Items based on the Tier %
  * Stat A list of spend: app.{*}.{*}.spend
  * Stat B sum for the same inventory spend from different dc: sumSeriesWithWildcards(#, 1)
  * Stat C find the inventory spend % among all spend: asPercent(#B)
  * Stat D get the inventories for the defined tiers, assuming higher than 10%: aliasByNode(averageAbove(#C, 10), 2)

* Step 2: Find the day to day spend comparison for all inventories:
  * Stat A list of spend: app.{*}.{*}.spend
  * Stat B list of today's spend: aliasByNode(sumSeriesWithWildcards(#A, 1), 2)
  * Stat C list of yesterday's spend: timeShift(#B, '1d', true, false)
  * Stat D find the day to day spend ratio: asPercent(#B, #C)

* Step 3: Find the day to day spend comparison based on the Tier % definition:
  * Stat A inventory list from Step 1 for the tier list
  * Stat B day to day spend comparison for all inventories
  * Stat C -1 weight for tier list inventories: scale(divideSeriesLists(#A, #A), -1)
  * Stat D get inventory comparison ratio with the weight remove values for no -1 weight from stat C: removeAboveValue(groupByNode(group(#C, #B), 0, 'multiply'), 0)
  * Stat E reverse the -1 weight and get the inventories comparision based on the tier % definition: aliasByNode(averageAbove(scale(#D, -1), 0), 0)
  
  
