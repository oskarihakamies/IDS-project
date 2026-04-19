# How to use Wazuh dashboards?

How to use dashboards in Wazuh?


<img width="1344" height="754" alt="Näyttökuva 2026-04-05 kello 19 41 05" src="https://github.com/user-attachments/assets/68a5e73a-6dcc-48ee-8656-273fcb070931" />


Go to the Dashboards and you should see this view


<img width="692" height="374" alt="Näyttökuva 2026-04-05 kello 19 41 38" src="https://github.com/user-attachments/assets/7c21b710-0f41-4dc5-af9f-e5dc39618e9d" />


From here you can add more data. 


<img width="1431" height="692" alt="Näyttökuva 2026-04-06 kello 14 48 25" src="https://github.com/user-attachments/assets/e493f926-fecf-4d7f-ad4e-573908117dce" />


From "Install some sample data" you can choose if you want more data to your dashboard.

-> if you choose to do that wait untill it says "Installed" in top of the paragraph. Otherwise it won´t use your sample data. 


<img width="809" height="605" alt="Näyttökuva 2026-04-06 kello 14 52 23" src="https://github.com/user-attachments/assets/e09356a3-04ff-4e12-8d43-dde68f5220f7" />


Next thing you get to choose is "New Visualization"

These next ones are important (in my opinion):

Data Table

- Shows raw data as a table
- When to use? : When wanting a aletrs as a list or list of recent events
- Gives you a great context in your dashboard

Line 

- Shows events as a time series and over time.
- When to use? : When wanting to know number of alerts over a time
- Biggest benefit is that you can clearly see if problem on the screen is growing or not

Verical Bar 

- When is it useful? : If you need to see top IP´s or attack types you are receiving
- In this option you get clear comparasion.

Pie 

- You get precentage distribuiton
- This is a great option if you want a quick overview from your data
- Would not recommend to use in bigger datasets

Heat Map 

- Shows you intensity, meanning color = ammount
- Login in attempts per day/hour/month
- Biggest advantage is when seeing spikes and anomalies in data

Goal 

- a gauge style mitter
- When to use? : If you want risk score or CPU usage
- Great again when checking status. (Ok or not OK)


In this example i used goal


<img width="708" height="261" alt="Näyttökuva 2026-04-06 kello 15 15 59" src="https://github.com/user-attachments/assets/526adbeb-cb9d-4626-b7f2-22112632b0d2" />

After you choose your visualzation your need to choose a source. This won´t work without a source. 


And my source was wazuh-alerts*


Now you see your dashboard! 

<img width="1469" height="807" alt="Näyttökuva 2026-04-06 kello 15 18 28" src="https://github.com/user-attachments/assets/adec724f-83a3-406b-a104-dea219fb7706" />

On the right side of your screen is everythin you need to modify your dashboard. There are many different options to choose.


For example i used these metrics and now it jumped many precentages upwards. (I used sample data when creating this visualization)


<img width="1449" height="730" alt="Näyttökuva 2026-04-06 kello 15 33 09" src="https://github.com/user-attachments/assets/d76e23e0-2d45-4e94-8ce4-b234df4dfdd5" />


After you are ready with your dashboard click "Save" on the right top corner. 

From here you can name your dashboard and write a description if you want.

<img width="402" height="409" alt="Näyttökuva 2026-04-06 kello 15 36 49" src="https://github.com/user-attachments/assets/8375c0f6-91e4-4556-a135-6b7c395a013a" />


And now you see it on your dashboard menu.


<img width="1425" height="718" alt="Näyttökuva 2026-04-06 kello 15 40 55" src="https://github.com/user-attachments/assets/da802408-b938-4df0-86b4-64b759719a2c" />

### Few examples:


In this scenario i see biggest threats that our service faced.

Output is data table and specific metrics for aggeregation is Max and for field it is rule.level

As you see on the screen there were 15 max level threats.

<img width="1460" height="525" alt="Näyttökuva 2026-04-06 kello 15 59 42" src="https://github.com/user-attachments/assets/948193f0-d92d-4198-82ba-ca97a30615a7" />


Here is a dashboard that i creted from our data

This dashboard will provide overview of security alerts collected by the Wazuh system over the selected time range 

1. On the left side of the screen is KPI metrics. Those tells us how bad the threat is.
2. Total alerts = number of alerts in selected timeframe
3. Aletrs over time = Line chart showing alerts across time, in this case only one spike which means that alerts are not continuous
4. Top Triggered Rules (Bottom Left) = Bar chart of most frequently triggered detection rules, that shows only that one rule dominates
5. Alert Severity Distribution (Bottom Right) = Pie chart showing proportion of alerts by severity meaning that this environment is relatively stable. 
 
 
<img width="1469" height="849" alt="Näyttökuva 2026-04-06 kello 17 09 33" src="https://github.com/user-attachments/assets/4d1fac56-623e-49f5-9dab-26e5c1545f1c" />

### Overall 

Based on this dashboard there are no no critical threats detected and majority of alerts are concentrated in medium severity. It is also good to konw that activty is low and not continuous. But keep in mind that data volume in this dashboard is small.


### Another Model

This one is made with Sample Malware Detection data. 

<img width="944" height="435" alt="Näyttökuva 2026-04-15 kello 14 23 00" src="https://github.com/user-attachments/assets/725352d7-d482-41aa-b708-aa0e2e846ca7" />



<img width="1470" height="738" alt="Näyttökuva 2026-04-15 kello 14 22 06" src="https://github.com/user-attachments/assets/518675b6-d0ed-4ecf-96e8-cc4223bb6bc6" />



## Logs and how to read them?

Now we know how to do basic dashboards. Next step is to understand them and make observaisons about them. Here is something close to a recap that you can try to. 


<img width="1300" height="506" alt="Näyttökuva 2026-04-19 kello 20 55 01" src="https://github.com/user-attachments/assets/011dc392-9f62-48cb-aa26-9e94f8217f13" />

In this picture we are at Wazuh Discover window, where we can see some alerts from the last 30 minutes of time. 

Basic info from this view:

- you can see timestamp for 30 minutes (20.24 - 20.54)
- hits = 89
- histogram = shows alarms or logs, you can see that the peak is around 20.35

