# Home Assistant-SOLVED - Calculate cost for individual device cost/kWh
`How to make sensors in Home Assistant to display consumption in kWh (day, week, month) and calculate the financial cost per device?`

This ended up to be (one, not the only) the working solution for me:

`I would like to calculate the cost of a particular device (in this case, Shelly, Zigbee and Tuya "energy monitor device").
My goal is to have a sensor that shows the total cost according to the use of the device and the corresponding daily, weekly and monthly price of electricity consumption.`

For my household/background information:
1.) First, name the device in a way that is understandable to you!
    
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

2.) Create one sensor that accumulates the wattage over time. 

`The entity/sensor to use is in W` `Be careful`, because some manufacturers also show how much the device consumes without a consumer. `Choose the right entity in W˛`

![W](https://github.com/Trzinka/HA-energy-cost/assets/40424965/62cc59d7-dc65-4c9e-97ea-74899f5a97eb)


3.) Then create a hourly utility sensor from the accumulation sensor. 

4.) Then multiply the price/hour-sensor with the utility sensor in the configuration-yaml file.



