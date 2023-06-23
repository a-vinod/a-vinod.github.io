---
layout: post
title:  "🚘 💡 DataDriven - UCSB CE Capstone 2023"
date:   2023-06-08
tags: project-announcement
---

DataDriven is an end-to-end vehicle data tracking system that 
1. collects vehicle data (vehicle speed, engine RPM, fuel levels, etc.), location data (latitude/longitude), and IMU data (acceleration in x/y/z)
2. uploads data to the cloud over LTE-M
3. processes data and serves it an an API
4. displays data on a front-end live with an interactive map
5. provides a data viz tool to view historic data in time series charts

Check out the demo!
<iframe id="video" width="560" height="315" src="https://www.youtube.com/embed/JWgRRGsqYg8/" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen=""></iframe>

Here is an overview of our system with a block diagram:
![](/assets/images/2023-06-08/blockdiagfinal_.png)

**Tracker Module**: steps (1) and (2) outlined above are accomplished by a custom PCB we designed that sits on the dash of the car. It collects data from and is powered by the vehicle's OBD-II diagnostic port. It gets location data and LTE-M access via the nRF9160 SoC's onboard LTE/GNSS modem. Data is aggregated and marshalled into a UDP datagram before being sent to a UDP server.
![](/assets/images/2023-06-08/pcb.png)

**Backend**: step (3) is achieved by a UDP server (dubbed **UDP Listener**) that receives data from the tracker, unmarshalls it, and decodes it before saving it to a database. An **API** (api.datadrivenucsb.com) then serves data from the database at various endpoints (e.g. /live/car_id for the latest data of a vehicle, /fetch_all/car_id for historical vehicle data). The UDP Listener and API are deployed on an EC2 instance on AWS and an RDS instance is used for the database.

**Frontend**: steps (4) and (5) are implemented with a front-end **Web App** developed in Flutter and deployed on Google's Firebase Hosting. The front-end has an interactive map that places markers as vehicle positions are updated live. It displays a sidebar with the latest vehicle data that was collected through the OBD-II port and IMU. The data visualization page allows users to query a specific vehicle parameter for vehicle(s) over a period of time and plot it in a time series chart.

For more details on the project and team, and access to the presentation materials (slides and a poster), check out our about page: [about.datadrivenucsb.com](https://about.datadrivenucsb.com/). 

This is a project for the [2023 UC Santa Barbara Computer Engineering Capstone](https://www.ce.ucsb.edu/undergrad/curriculum/capstone/events/ece189/2023) program.

Awards: 🏆 Distinguished Technical Achievement in CE (2nd Place)
