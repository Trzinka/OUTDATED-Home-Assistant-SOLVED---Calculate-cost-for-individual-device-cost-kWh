# Home Assistant-SOLVED - Calculate cost for individual device cost/kWh
`How to make sensors in Home Assistant to display consumption in kWh (day, week, month) and calculate the financial cost per device?`

***To get a similar look:***

![Obračun tm](https://github.com/Trzinka/HA-energy-cost/assets/40424965/458193b2-054f-4c52-968c-26571ddf027b)

***ore this:***

![Obračun me](https://github.com/Trzinka/HA-energy-cost/assets/40424965/aaaf2c9f-585c-4916-a2b9-45714ef76c14)


This ended up to be (one, not the only) the working solution for me:

`I would like to calculate the cost of a particular device (in this case, Shelly, Zigbee and Tuya "energy monitor device").
My goal is to have a sensor that shows the total cost according to the use of the device and the corresponding daily, weekly and monthly price of electricity consumption.`

For my household/background information:




## 1.) First, name the device in a way that is understandable to you!
******************************************************************
    
    My example (Slovenian language)
        Me = energy meter
        Tm = temperature gauge
        Bo = boiler
        Na = daughter's room
![#1-Shelly-Privzeto](https://github.com/Trzinka/HA-energy-cost/assets/40424965/36e62ab4-26d3-42aa-a5ec-c6aea5407f9b)
`Different manufacturers call entities differently!`

![#2-Shelly-Spremenjeno](https://github.com/Trzinka/HA-energy-cost/assets/40424965/28ca19d1-4a19-44a5-8945-932f2a2bab4f)
![#2-Shelly-Spremenjeno2](https://github.com/Trzinka/HA-energy-cost/assets/40424965/1723de46-02ea-4576-b17d-373db79c9d0a)
![#2-Tuya-Spremenjeno](https://github.com/Trzinka/HA-energy-cost/assets/40424965/fdfd9944-d17c-4df6-8e96-3c2b0c25bf53)
![#2-Zigbee-Spremenjeno](https://github.com/Trzinka/HA-energy-cost/assets/40424965/eab1daab-32f2-4467-8e07-b739a065c060)




## 2.) Create one sensor that will determine the price/kWh of electricity and the monthly fixed cost of the electrical grid connection. 
***************************************************************************************************************************************

`The entity/sensor to use is in W` `Be careful`, because some manufacturers also show how much the device consumes without a consumer. `Choose the right entity in W˛`

![W](https://github.com/Trzinka/HA-energy-cost/assets/40424965/62cc59d7-dc65-4c9e-97ea-74899f5a97eb)

Enter the following code in the sensors.yaml file by changing the amount:

    "Electricity price €/kWh"
    value_template: 
and 
    
    "Fixed monthly electricity price"
    value_template:
        
to match your costs.

***WRITE THIS TWO SENSORS:***         
```
#================================================
# Electricity billing
#================================================ 
   - platform: template
     sensors:
       electricity_kwh_unit_price:
         friendly_name_template: "Electricity price €/kWh"
         value_template: "0.27808" # (Electricity ET, Network charge ET, Contribution for the operation of the market operator, Contribution for energy efficiency and Excise duty)
         unit_of_measurement: EUR/kWh
         unique_id: ?

       electricity_standing_charge_price:
         friendly_name_template: "Fixed monthly electricity price"
         value_template: "16.3113" # (Accounting power, Contribution for CHP and RES, Flat cost of business and minus E-discount)
         unit_of_measurement: EUR
         unique_id: ?
```


For "unique_id:" use (https://www.guidgenerator.com/online-guid-generator.aspx) to generate it.


If you don't know what I'm writing about, take a look: `https://youtu.be/kgFwhYInzfA`, or something similar.

After the system restarts, you should see something like this:

![Cena elektrike €-kWh](https://github.com/Trzinka/HA-energy-cost/assets/40424965/0a3b797a-dc8c-41b7-9187-a2549443e9a9)

and this:

![Fiksna cena elektrike mesečno](https://github.com/Trzinka/HA-energy-cost/assets/40424965/d3f005d9-8024-43c8-a911-4e01972344bf)

***Now we have the sensor we created "Electricity price €/kWh" and "Fixed monthly electricity price", and we can continue to the next point.***




## 3.) Then, from the device entity in W, create the , from which we will later obtain the desired data.
**********************************************************************************************************************************
You will need Integration - Riemann sum integral for the next step. 

If you don't have it installed, see: (https://www.home-assistant.io/integrations/integration/)

Simply click on the `add integration to' button. Follow the instructions. Restart the system if necessary!

`Go and click on settings, then on devices & services and then on helpers`. Click on:
![#3-Helper](https://github.com/Trzinka/HA-energy-cost/assets/40424965/b49d8e0e-56ad-47ca-b0ba-84757787ee8c)

then choose:

![#4-Integration](https://github.com/Trzinka/HA-energy-cost/assets/40424965/42f7e143-a04b-4deb-807a-8966f469216f)

and then select the desired entity in W:

![#5-Select](https://github.com/Trzinka/HA-energy-cost/assets/40424965/58edb5a3-cc30-48fb-98de-d3ab3a72d9e6)

then select marked values:

![#6-Setings](https://github.com/Trzinka/HA-energy-cost/assets/40424965/84e962aa-d9e2-433f-9e86-3debd05ebc0b)

Repeat the process for all desired entities. After the process is complete, you should get something like this:

![#7a-Change icon](https://github.com/Trzinka/HA-energy-cost/assets/40424965/d52b5838-8afb-4b25-b118-2df49d17fb1a)

If the appearance of the icons bothers you (like me), follow the instructions. Click on the helper whose icon you want to change:
![#7a-Change icon](https://github.com/Trzinka/HA-energy-cost/assets/40424965/0566d580-a81f-4f0b-a121-d37d4f514bc3)

Click on settings (as indicated in the image):
![#7b-Change icon](https://github.com/Trzinka/HA-energy-cost/assets/40424965/8d4c730a-028f-447e-b070-f3f2c20acfd3)

Change to the desired icon:
![#7c-Change icon](https://github.com/Trzinka/HA-energy-cost/assets/40424965/4c0a60d7-00fd-48be-a483-7393a41fbf55)

The end result should be something like this:
![#8a-Utility](https://github.com/Trzinka/HA-energy-cost/assets/40424965/53fbc0ec-0257-4d9c-afce-c5eede1d0dd0)

***Now we have the sensor we created for each entity we wanted to create the accumulation entity in kWh, and we can continue to the next point.***




## 4.) Then create a day, week, month (if you want you can also do for hourly) utility sensor from the accumulation sensor.
************************************************************************************************************************
You will need Utility Meter integral for the next step. 

If you don't have it installed, see: (https://www.home-assistant.io/integrations/utility_meter/)

Simply click on the `add integration to' button. Follow the instructions. Restart the system if necessary!

`Go and click on settings, then on devices & services and then on helpers`. Click on:

![#3-Helper](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/2646d48b-9ab4-407a-b09f-7baed256ddcc)

then choose:

![#8b-Utility](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/a88430f5-b2f8-498f-a514-2e5db2e0e991)

select the previously made sensor accumulation entity in kWh, and select the time period (day, week, month) repeat each time separately.

![#8c1-Utility](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/014a5cca-08dd-47e3-a435-7010c0b664ac)
![#8c2-Utility](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/4103c2a7-ab21-4913-b950-6204e405ed95)
![#8c-Utility](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/65cef096-1e1c-4446-a613-227773350d44)

The end result should be something like this:

![Meritve](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/e1c2e8da-4e0d-45d7-bc2c-ce34aebb7023)


and for the device

![Me-Bo](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/8525e494-0b1d-4794-846b-bf4318cd15d7)

***Now we have the sensor we created for each entity we wanted to create the accumulation entity for day, week, month in kWh, and we can continue to the next point.***




## 5.) Then multiply the price/kwh-sensor with the utility sensor in the sensors.yaml file.
*******************************************************************************************
Enter the following code in the sensors.yaml file:

***WRITE THIS THREE SENSORS:***         
```
#================================================ ================================================= ================================================= ====
# Electricity billing by consumer
#================================================ ================================================= ================================================= ====
#1                                                                 Shelly
   - platform: template
     sensors:
       consumption_me_will_be_daily:
         friendly_name_template: "Daily boiler consumption"
         unit_of_measurement: "€"
         value_template: "{{ (states('sensor.me_bo_poraba_kwh_dnevno') | round (2)) * (states('sensor.electricity_kwh_unit_price') | round (2))}}"
         icon_template: mdi:currency-eur
         unique_id: ?
   - platform: template
     sensors:
       spending_me_will_be_weekly:
         friendly_name_template: "Weekly boiler consumption"
         unit_of_measurement: "€"
         value_template: "{{ (states('sensor.me_bo_poraba_kwh_tendensko') | round (2)) * (states('sensor.electricity_kwh_unit_price') | round (2))}}"
         icon_template: mdi:currency-eur
         unique_id: ?
   - platform: template
     sensors:
       spending_me_will_be_monthly:
         friendly_name_template: "Monthly boiler consumption"
         unit_of_measurement: "€"
         value_template: "{{ (states('sensor.me_bo_poraba_kwh_mesecno') | round (2)) * (states('sensor.electricity_kwh_unit_price') | round (2))}}"
         icon_template: mdi:currency-eur
         unique_id: ?
```

For "unique_id:" use (https://www.guidgenerator.com/online-guid-generator.aspx) to generate it. It can generate multiple numbers at once!

The end result should be something like this:
![Me-Bo EUR](https://github.com/Trzinka/Home-Assistant-SOLVED---Calculate-cost-for-individual-device-cost-kWh/assets/40424965/b7ab29db-eeae-4532-b64c-8cb2f8c32951)


## 6.) Now you can make a Lovelace card.
****************************************




# 7.) look for more (https://www.youtube.com/results?search_query=homeassistant+calculating+electricity+price) 

and much more 


(https://www.google.com/search?sca_esv=591524815&sxsrf=AM9HkKmIuV66qLgKD2uXqiGiF19H7qV4_w:1702746085001&q=home+assistant+calculating+electricity+price&spell=1&sa=X&ved=2ahUKEwjN2ae8t5SDAxWYSfEDHUV6BpwQBSgAegQICBAC&biw=1366&bih=645&dpr=1)
