+++
title = 'Tuya Zigbee Valve Power Supply Conversion'
date = 2024-05-07T16:05:21+10:00
draft = false
categories = ['IoT']
tags = ['zigbee', 'fw', 'how-to']
+++

# Converting Zigbee Irrigation Valves from Battery to Regulated Power Supply: A Step-by-Step Guide

## Introduction
This is a short blog post about converting Zigbee irrigation valves (pictured below) powered by batteries to a more reliable and sustainable regulated power supply. This upgrade can reduce maintenance efforts, and ensure a continuous power source for your smart irrigation system. In this guide, I'll walk you through the process, step by step.

[Tuya Zigbee Smart Watering Timer Smart Sprinkler Drip Irrigation System Built-in Water Flow Recorder Water Controller](https://www.aliexpress.com/item/1005005196816776.html?spm=a2g0o.order_list.order_list_main.40.14971802Z6sJ8Y)

![tuya-zigbee-valve-controller](/static/tuya-zigbee-valve-controller.png)

<img src="https://github.com/lennon101/blogs/assets/1709944/359a96d9-2ffc-48d4-a4be-6a1f232272ed" width="200">


## Why Upgrade to Regulated Power Supply?
This was in response to the batteries of these devices draining very quickly and requiring frequent replacements. Upgrading to a regulated power supply ensures a stable and continuous power source, reducing the need for constant maintenance and enhancing the overall reliability of your Zigbee irrigation system.

## Understanding Zigbee Irrigation Valves
Before diving into the conversion process, it's essential to have a basic understanding of how Zigbee irrigation valves work and the power requirements associated with them. 
Each device is powered by 4x AA batteries in series. Each AA battery is 1.5V at 100%, therefore, the total voltage needed for this device is:

`total voltage = 4 x 1.5V` 

Therefore: 
`total voltage = 6v` 

### Current (amps) requirement: 
At this stage, I am not aware of the current requirement of these devices, but a typical AA battery has a supply peak currents of over 2A. But I know (just from my own experience) that these zigbee devices won't be drawing that much. So lets say approx 1amp max current during peak demands (and that is being very generous). 

## Materials Needed
1. An AC to DC power supply with atleast 6v and 1amp output. A typical looking power supply is pictured below,
2. A linear regulator - only needed if you your power supply has an output greater than 6v. A good example is the LM317 Adjustable Voltage Regulator Power Supply (see image below). Just go to AliExpress and search `LM317 Breakout Board`. An example is shown below. 
3. A small plastic container to store the linear regulator in (if you need one). 
4. A multimetre to check the voltage 

<img src="https://github.com/lennon101/blogs/assets/1709944/98392c22-187b-40c4-8111-9f88e4b1f851" width="300">

<img src="https://github.com/lennon101/blogs/assets/1709944/cb2020f6-52a5-416d-a75a-bd8d5d5f5ef8" width="200">



## Step-by-Step Process

### 5.1 Disassembling the device
Begin the conversion process by carefully disassembling the Zigbee irrigation valve. Take note of the existing components and their placements.

### 5.2 wiring diagram
<img src= "https://github.com/lennon101/blogs/assets/1709944/d3f62dcc-7176-4edd-811e-4bba564ccdfe" width="500">


### 5.3 Selecting a Regulated Power Supply
Explore different regulated power supply options and select the one that best fits your requirements. Consider factors such as voltage, current, and environmental suitability.

### 5.4 Modifying the Valve Circuit
Learn how to modify the valve circuit to accommodate the regulated power supply. This step may involve rewiring and integrating the new power source seamlessly.

### 5.5 Assembling the System
Put everything back together, ensuring all components are properly connected. Follow a systematic approach to avoid any issues during the assembly.

## Testing the Upgraded System
After the conversion, thoroughly test the system to ensure that it functions as expected. Address any issues that may arise during the testing phase.

## Benefits of Regulated Power Supply
Explore the advantages of upgrading to a regulated power supply, including increased reliability, reduced maintenance costs, and improved overall performance.

## Troubleshooting Tips
In case you encounter any issues during or after the conversion, refer to our troubleshooting tips to identify and resolve potential problems.

## Conclusion
Summarize the key points discussed in the blog post and emphasize the benefits of upgrading Zigbee irrigation valves to a regulated power supply.

## Additional Resources
Provide links to relevant resources, such as recommended power supplies, circuit diagrams, and related articles, to further assist readers in their conversion journey.