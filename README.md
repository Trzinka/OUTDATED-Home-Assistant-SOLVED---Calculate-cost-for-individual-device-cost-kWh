# Home Assistant-SOLVED - Calculate cost for individual device cost/kWh
`How to make sensors in Home Assistant to display consumption in kWh (day, week, month) and calculate the financial cost per device?`

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



## 2.) Create one sensor that accumulates the wattage over time. 
*************************************************************

`The entity/sensor to use is in W` `Be careful`, because some manufacturers also show how much the device consumes without a consumer. `Choose the right entity in W˛`

![W](https://github.com/Trzinka/HA-energy-cost/assets/40424965/62cc59d7-dc65-4c9e-97ea-74899f5a97eb)

Enter the following code in the sensors.yaml file by changing the amount 

    "Electricity price €/kWh"
    value_template: 
and 
    
    "Fixed monthly electricity price"
    value_template:
        
to match your costs.

### WRITE THIS TWO SENSORS:         
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
         unique_id: 9b434ad5-7c23-4d49-8ffa-a30c17e2084a

       electricity_standing_charge_price:
         friendly_name_template: "Fixed monthly electricity price"
         value_template: "16.3113" # (Accounting power, Contribution for CHP and RES, Flat cost of business and minus E-discount)
         unit_of_measurement: EUR
         unique_id: d462bb14-36d7-4d2c-bb04-5cf5f1767b52
```

If you don't know what I'm writing about, take a look: `https://youtu.be/kgFwhYInzfA`, or something similar.

After the system restarts, you should see something like this:

![Cena elektrike €-kWh](https://github.com/Trzinka/HA-energy-cost/assets/40424965/0a3b797a-dc8c-41b7-9187-a2549443e9a9)

and this:

![Fiksna cena elektrike mesečno](https://github.com/Trzinka/HA-energy-cost/assets/40424965/d3f005d9-8024-43c8-a911-4e01972344bf)

Now we have the sensor we created "Electricity price €/kWh" and "Fixed monthly electricity price" and we can continue to the next point.



## 3.) Then create a day, week, month (if you want you can also do for hourly) utility sensor from the accumulation sensor.
************************************************************************************************************************
You will need Integration - Riemann sum integral for the next step. 

If you don't have it installed, see: (https://www.home-assistant.io/integrations/integration/)

Simply click on the `add integration to' button. Follow the instructions. Restart the system if necessary!

Go and click on settings, then on devices & services and then on helpers. Click on:
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




## 4.) Then multiply the price/hour-sensor with the utility sensor in the configuration-yaml file.
**************************************************************************************************



