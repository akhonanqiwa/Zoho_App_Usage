# ZoZo Inc App Usage Analysis
## About the project
The Business (ZoZo Inc) has made some of their App data available and they need insights about the usage of their app by patrons. 
The dataset given contains information about the devices used to access the app, click information, patron unique identification number, the app version and the gps data.

## Data Sources:
The dataset consists of 1 spreadsheet. 
•	“Assessment Data.csv”
## Tools
  •	SQL SERVER -Data Analysis and Data Manipulation
  •	Power Query – ETL (data sorting, data transformation)
  •	Power Pivot - ERD diagram (table joins/relationships for data modeling)
  •	PowerBI - Data Visualization (dashboard)
  •	DAX - Data aggregation

## Data Preparation
- After examining and extracting the data from the “Assessment Data.zip” file, which contains one CSV file.                                   
 •	I imported the data to SQL SERVER database and created a table ..dbo.Zoho_App where I noticed unique ID’s patron_id and device_id.                                  
 •	There were values like gsp_lat,gps_alt that contained null values which I then changed the null values to 0.                                                                
- Importing data to SQL SERVER was 1 of the ways to explore different tools for data analysis.                                                                                      
 •	I then uploaded the data into Power Query Editor for cleaning and transformation.                                                                                                                                
 •	In this process, I replaced null values with 0 and utilized the first row as headers.

## Exploratory Data Analysis
  1.	How many unique patrons do they have using their ZoZo App? 
  2.	How many devices do the patrons use on average? 
  3.	Plot on a heat map the platform most used with ability to choose certain OS versions interactively
  4.	Show the top 10 most used make/model per platform
  5.	Is there a correlation between the device platform and the altitude of where the device is used?
  6.	Which is the most used version of the ZoZo App? 
  7.	Plot over time how many patrons use which platform.

## Data Analysis
- Writing SQL queries to perform data aggregation and creating DAX functions in POWERBI to create measures for calculations.                           
- The below sql query shows the total number of patrons that are using the Zoho App                            
    Select count(distinct[patron_id]) FROM ..[dbo].[Zoho App]                                              
- The below DAX function also shows the total number of patrons that are using the app                          
    patrons_usage = CALCULATE(DISTINCTCOUNT(Sheet1[patron_id]))                                                  
- The below shows the number of devices do the patrons use on average in both SQL and DAX                             
    SQL:  with avgg as                                                         
       (                               
 				select [patron_id],count([device_id]) count_of_deviceID                  
         FROM [RPT_Premier].[dbo].[Zoho App]                  
 				 group by [patron_id]                  
      )                            
    select avg(count_of_deviceID) average_devices_per_patron                
    from avgg
                                             
		DAX : 	ave_device_count = AVERAGEX(VALUES(Sheet1[device_id]),                                     
            CALCULATE(DISTINCTCOUNT(Sheet1[device_id])))                                                
- The below shows the heat map the platform most used with ability to choose certain OS versions.                       
    SQL : 	CREATE VIEW Pivot_Table AS                            
            SELECT device_platform, device_os, COUNT(patron_id) AS patron_count               
            FROM [RPT_Premier].[dbo].[Zoho App]Zoho_App                       
            GROUP BY device_platform, device_os                        
            order by patron_count desc;                                          
- The below shows the top 10 most used make/model per platform                         
    SQL: SELECT top 10 make_model, COUNT(*) AS device_count                             
          FROM [RPT_Premier].[dbo].[Zoho App]                                         
          GROUP BY device_platform,make_model                                                       
          ORDER BY device_count DESC;                                                   

- The most use app version                                                                
    SQL: SELECT [app_ver], COUNT(device_id) AS usage_count                                                              
          FROM [RPT_Premier].[dbo].[Zoho App]                                                                         
          WHERE device_id IN (select device_id from [RPT_Premier].[dbo].[Zoho App])                                                                            
          GROUP BY [app_ver]                                                                         
          ORDER BY usage_count DESC                                                                                                         

- The correlation between the device platform and the altitude of where the device is calculated using DAX function as SQL does not support this.                                          
      DAX:  DeviceCountPerLatitude =                                                                                    
            CALCULATE(                                                                             
   				       DISTINCTCOUNT(Sheet1[device_id]),                                                         
    					    ALLEXCEPT(Sheet1, Sheet1[gps_lat])                                                                                
              )                                                                                                          
- The count of patrons by each device paltform they used most.                                                                                      
  SQL: select device_platform,count(patron_id) patron_count                                                                                
        FROM [RPT_Premier].[dbo].[Zoho App]                                                                                
        group by device_platform                                                                                                  
        order by patron_count desc                                                                                     

## Findings/Results using PowerBI visuals:
### Refer to the dashboard screenshot for below answers.

- The screenshot shows the dashboard created to answer the business question.       
- There are 76k patrons actively using the Zoho App which only 1 number of device in average is used to access the app.              
- The diagram with colors is a heatmap that shows the platform most used with ability to choose certain OS versions interactively.            
  You will notice blue which defines the highest number of patrons using that particular model and it’s os version and the lower number remains orange as the background.    
  You will also notice that 71362 patrons are using Android os version 23 whereas there are 2 more os upgrades available.     
- The bottom table shows the top 10 most used models by the patrons which the advance filter was used to set the table to show only the top 10 by the count of the patron’s unique id.
  This shows that majority of patrons prefer using iPhone 6S.        
- The bar graph diagram shows the most used app version by counting the devices that install the version of the app.
  This shows that patrons are still lacking in upgrading app versions in time seeing that the majority still uses version 16 whereas there is a new version 17.              
- The donut chart show the total number of patron by each device platform used and this tells that Android is the most used device platform followed by iPhone.    

## FURTHER ANALYSIS I WOULD CONSIDER/CONCLUSION
•	I would further analyze key metrics like "click date and time" to get an understanding of the distribution of clicks overtime and peak usage times.    
•	The frequency of clicks per patron by their unique id.                 
•	How frequently time frame does the app gets to have a new version in a year or 6 month’s time interval.                            
•	Lastly, I would further analyze the accuracy of GPS data.               
