---
title: "Storm Watch Dashboard"
description: "Home Assistant dashboard for real-time weather monitoring in Townsville using Ecowitt data, BOM radar, ApexCharts, Plotly, and Mushroom cards."
date: 2025-08-01
categories: ["wiki"]
tags: ["Home Automation"]
draft: false
---

# Storm Watch Dashboard  

This Home Assistant dashboard, **Storm Watch**, is designed to monitor weather conditions in real time. Living in Townsville, I built this dashboard to keep an eye on the intense rainfall we're experiencing.  

## Data Sources  

- **Ecowitt Weather Station** – All weather data is sourced from my personal Ecowitt station, providing up-to-date local conditions.  
- **Rain Radar Integration** – The rain radar card is a custom card created using the **bom-radar-card**.  

## Dashboard Features  

- **Live Weather Metrics** – Displays temperature, humidity, wind speed, and barometric pressure.  
- **Rainfall Tracking** – Monitors real-time and cumulative rainfall measurements.  
- **Rain Radar Overlay** – Shows live radar imagery for tracking incoming weather.  

## Home Assistant Setup  

This dashboard is built using Lovelace with a combination of built-in cards and custom integrations.  

<img width="1443" alt="Screenshot 2025-02-03 at 7 59 47 am" src="https://gist.github.com/user-attachments/assets/8daff51e-82dd-4edd-a340-a627eb132e80" />

---
Below is the yaml used to create this card: 

<img src="https://gist.github.com/user-attachments/assets/0e34ef35-5f34-4c20-b7b9-5159e5b17c40" alt="image" width="200"/>


```yaml
graph: line
type: sensor
entity: sensor.gw1100c_hourly_rain_rate
detail: 1
name: Rain the last hour

```

The second graph beside it requires a statitics sensor to calculate a rolling 24hr total of rain (rain in the last 24hrs). This was done like this:

1. Navigate to the **Helpers** in HA --> Add new Helper --> Statistics Helper 
2. Name your new Sensor 
3. Use the `GW1100C Total Rain` sensor as the base sensor 
4. Select the **Change** Statistics characters (calculates the delta between the newest and oldest data) 
5. Set the **Max age** to 24hrs and leave everything else alone. See below. 


<img src="https://gist.github.com/user-attachments/assets/bf9acf60-707a-4e4d-8c86-732cb10fdb0a" alt="image" width="200"/>

Now use this new statistics sensor in this yaml:

```yaml
graph: line
type: sensor
entity: sensor.rain_last_24_hour
detail: 2
name: Rain Last 24 Hours
icon: mdi:clock-time-nine-outline

```

---
The Column plot uses a custom integration called ApexCharts. Install it using **HACS** then add the following yaml to your dashboard 

<img src="https://gist.github.com/user-attachments/assets/21fbec59-aead-440b-91d6-7412b2744e24" alt="image" width="200"/>

```yaml
      type: custom:apexcharts-card
      graph_span: 7d
      span:
        end: day
      header:
        show: true
        title: 'Daily rain totals'
        show_states: true
        colorize_states: true
      apex_config:
        plotOptions:
          bar:
            columnWidth: 80%
            borderRadius: 2
      series:
        - entity: sensor.gw1100c_daily_rain_rate
          name: 'Total Rain'
          color: '#FCD663'
          type: column
          show:
            extremas: true
          group_by:
            func: max
            duration: 24hr
      all_series_config:
        stroke_width: 2
        opacity: 1
        float_precision: 2
```


---
For the Rain Event plot I used another custom integration called **plotly-graph**. Again you get this from **HACS**. The yaml is below. 

<img src="https://gist.github.com/user-attachments/assets/ac403179-0a44-41d7-bf63-33519535d3b4" alt="image" width="200"/>

```yaml
type: custom:plotly-graph
time_offset: 2h
entities:
  - entity: sensor.gw1100c_rain_rate
    fill: tozeroy
    show_value: true
  - entity: sensor.gw1100c_event_rain_rate
    show_value: true
hours_to_show: 12h
refresh_interval: 10
title: Rain Event Data
layout:
  xaxis:
    rangeselector:
      "y": 1.2
      buttons:
        - count: 1
          step: minute
        - count: 1
          step: hour
        - count: 12
          step: hour
        - count: 1
          step: day
        - count: 7
          step: day

```

---
The card below was created using a combination of Mushroom Template and Mushroom Entity Cards. You can get the mushroom cards from HACS as well :)

<img src="https://gist.github.com/user-attachments/assets/d2dda79c-0890-4ba3-8d53-1584734f5062" alt="image" width="200"/>

To get the details of when the rain event began and how long it has been going for, you'll need to create three extra sensors. This is shown below. Unlike the other sensors, these are template sensors. Take note! For more information on these **Trigger Based Template Sensors** see the official HA documentation here: [Trigger Based Template Sensors](https://www.home-assistant.io/integrations/template/#trigger-based-template-binary-sensors-buttons-images-numbers-selects-and-sensors)


```yaml 
template:
      # store the datetime object when the event event sensor rises above 0  
      - trigger:
        - platform: numeric_state
          entity_id: sensor.gw1100c_event_rain_rate
          above: 0
        sensor:
          - name: "Rain Event Began Timestamp"
            unique_id: rain_event_began_timestamp
            state: "{{now()}}"

      # Trigger based template sensor that updates when rain event goes above 0 or is set to 0 
      - trigger:
        - platform: numeric_state
          entity_id: sensor.gw1100c_event_rain_rate
          above: 0
        - platform: state
          entity_id: sensor.gw1100c_event_rain_rate
          to: '0'
        sensor:
          - name: "Date Rain Event Began"
            unique_id: date_rain_event_began
            state: "{{ now().strftime('%a %d-%b %H:%M') if trigger.platform == 'numeric_state' else 0 }}"

       # use the stored datetime object above to calculate relative time since the rain event sensor rose above 0 
      - trigger:
        - platform: time_pattern  # update the sensor every 60 seconds 
          seconds: "/59"    
        - platform: numeric_state # update the sensor when the rain event raises above 0 
          entity_id: sensor.gw1100c_event_rain_rate
          above: 0
        sensor: 
          - name: "Time Since Rain Event Began" 
            unique_id: time_since_rain_event_began
            state: "{{as_datetime(states('sensor.rain_event_began_timestamp')) | relative_time }}"

```

The complete vertical stack is below: 

```yaml
type: vertical-stack
cards:
  - type: conditional
    conditions:
      - condition: state
        entity: sensor.gw1100c_event_rain_rate
        state_not: "0.0"
    card:
      type: markdown
      content: >-
        <ha-icon icon="mdi:weather-pouring"></ha-icon> Rain event started
        {{states('sensor.time_since_rain_event_began_2')}} ago on 
        {{states('sensor.date_rain_event_began')}}
           
  - type: conditional
    conditions:
      - condition: state
        entity: sensor.gw1100c_event_rain_rate
        state: "0.0"
    card:
      type: markdown
      content: >-
        <ha-icon icon="mdi:weather-partly-cloudy"></ha-icon> Last rain event
        started {{states('sensor.time_since_rain_event_began_2')}} ago on 
        {{states('sensor.date_rain_event_began')}}
           
  - square: false
    columns: 2
    type: grid
    cards:
      - type: vertical-stack
        cards:
          - type: custom:mushroom-entity-card
            entity: sensor.gw1100c_daily_rain_rate
            name: Daily Rain
            icon: mdi:weather-pouring
            fill_container: false
            hold_action:
              action: more-info
          - type: custom:mushroom-entity-card
            entity: sensor.gw1100c_rain_rate
            name: Rain Rate
            icon: mdi:chart-line-variant
          - type: custom:mushroom-entity-card
            entity: sensor.gw1100c_event_rain_rate
            icon: mdi:pail-outline
            name: Event Total
            fill_container: false
            hold_action:
              action: more-info
            double_tap_action:
              action: more-info
      - type: entities
        entities:
          - entity: sensor.gw1100c_hourly_rain_rate
            name: Hourly
            card_mod:
              style:
                hui-generic-entity-row:
                  $: |
                    state-badge {
                      display: none;
                    }
          - entity: sensor.gw1100c_weekly_rain_rate
            name: Weekly
            card_mod:
              style:
                hui-generic-entity-row:
                  $: |
                    state-badge {
                      display: none;
                    }
          - entity: sensor.gw1100c_monthly_rain_rate
            name: Monthly
            card_mod:
              style:
                hui-generic-entity-row:
                  $: |
                    state-badge {
                      display: none;
                    }
          - entity: sensor.gw1100c_yearly_rain_rate
            name: Yearly
            card_mod:
              style:
                hui-generic-entity-row:
                  $: |
                    state-badge {
                      display: none;
                    }

```

---

Lastly, the yaml for the rain radar card is below. The latitude/longitude will need to be change to suit your location

```yaml
type: custom:bom-radar-card
frame_count: 10
center_latitude: -19.25866756957461
center_longitude: 146.78399254023006
marker_latitude: -19.25866756957461
marker_longitude: 146.78399254023006
show_marker: true
show_range: true
show_zoom: true
show_recenter: true
show_playback: true
zoom_level: 8
show_radar_location: false
show_radar_coverage: false
map_style: Satellite
data_source: RainViewer-Original
square_map: true
radar_location_radius: 20


```