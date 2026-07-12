# Azure App Service and Autoscale Deployment

> **Disclaimer:** All Azure resources created in this lab were deleted after completion. No services were left running. This lab was built purely for learning and documentation purposes.

---

## Description

This lab focuses on deploying a web application using Azure App Service and configuring Custom Autoscale rules based on CPU utilization — covering App Service Plan creation, code deployment, and scale-out/scale-in automation.

| Detail                  | Value                          |
| ----------------------- | ------------------------------ |
| **Resource Group**      | `Whare1`                       |
| **Region**              | `Australia East`               |
| **App Service Plan**    | `Kawana Mg`                    |
| **Web App**             | `Web-mahi1`                    |
| **Runtime Stack**       | `Node 24 LTS`                  |
| **Autoscale Min/Max**   | `1 / 3 instances`              |
| **Scale-Out Trigger**   | `CPU > 70%`                    |
| **Scale-In Trigger**    | `CPU < 30%`                    |

---

## What I Covered in This Lab

- Created a Resource Group to contain all lab resources
- Created an App Service Plan on the P0v4 tier (required for Autoscale support)
- Deployed a Web App (App Service) and confirmed it was reachable 
- Deployed application code to the Web App
- Configured Custom Autoscale with a scale-out rule (CPU > 70%) and a scale-in rule (CPU < 30%)
- Generated load against the app to trigger and observe autoscale behaviour
- Reviewed the Autoscale run history to confirm scaling events fired correctly
---

## Lab Structure

```
azure-appservice-autoscale-lab/
├── README.md
└── screenshots/
    ├── part1-resource-group/
    ├── part2-app-service-plan/
    ├── part3-web-app/
    ├── part4-deployment/
    └── part5-autoscale/
```

---

## Part 1 — Resource Group

I created a Resource Group to hold all resources for this lab, keeping it isolated from other lab environments.
 
| Detail              | Value                      |
| -------------------- | -------------------------- |
| **RG Name**          | `Whare1`                   |
| **Region**           | `Australia East`    


<img src="https://imgur.com/vvDSr4x.png" height="80%" width="80%" alt="RG"/>

---
 
## Part 2 — App Service Plan

I created an App Service Plan to define the compute tier and hosting capacity for the Web App. I the Premium P0v4 tier, as the Free and Shared tiers do not support Autoscale.
 
| Detail              | Value                          |
| ------------------- | ------------------------------- |
| **Plan Name**       | `Hei-mahi-1`                    |
| **OS**              | `Linux`                         |
| **Pricing Tier**    | `Premium P0v4`                 |
| **Resource Group**  | `Whare1`    

**Steps:**
 
1. Navigated to **App Service plans** → **+ Create**
2. Resource Group: **Whare1**
3. Name: **Hei-mahi-1**
4. Pricing Tier: **Premium P0v4**
5. Reviewed and created

<img src="https://imgur.com/HsuiBvX.png" height="80%" width="80%" alt="Service Plan"/>

---
 
## Part 3 — Web App (App Service)

I created the Web App and attached it to the existing App Service Plan.
 
| Detail              | Value                          |
| ------------------- | ------------------------------- |
| **App Name**        | `Web-mahi`                      |
| **Publish**         | `Code` / `Docker Container`      |
| **Runtime Stack**   | `Node 24 TLS`                  |
| **App Service Plan**| `Hei-mahi-1`                    |
| **Default Domain**  | `web-mahi.azurewebsites.net`     |
 
<img src="https://imgur.com/OrRZcu9.png" height="80%" width="80%" alt="Web App"/>

Confirmed the app loaded successfully on its default domain.

<img src="https://imgur.com/Pnubiyg.png" height="80%" width="80%" alt="Web App"/>

---
 
## Part 4 — Deployment

I deployed application code to `Web-mahi` using the Deployment Center.
 
| Detail              | Value                              |
| ------------------- | ------------------------------------ |
| **Deployment Method**| `GitHub Actions`                    |
| **Source**          | `Azure-App-Service-and-Autoscale-Deployment`              |

**Steps:**
 
1. Navigated to **Web-mahi** → **Deployment Center**
2. Selected deployment for Github
3. Connected source
4. Verified deployment completed and app reflected the new code
 
<img src="https://imgur.com/PqHgB2U.png" height="80%" width="80%" alt="Deploy"/>

---
 
## Part 5 — Autoscale Configuration
 
I configured Custom Autoscale on the App Service Plan to automatically add and remove instances based on CPU load.
 
| Detail                | Value            |
| ---------------------- | ---------------- |
| **Min Instances**      | `1`              |
| **Max Instances**      | `3`              |
| **Default Instances**  | `1`              |
| **Scale-Out Rule**     | `CPU > 70% → +1 instance` |
| **Scale-In Rule**      | `CPU < 30% → -1 instance` |
 
**Steps:**
 
1. Navigated to **Hei-mahi-1** → **Scale out (App Service plan)**
2. Selected **Custom autoscale**
3. Set instance limits: Min **1**, Max **3**, Default **1**
4. Added scale-out rule: CPU Percentage > 70% → Increase count by 1
5. Added scale-in rule: CPU Percentage < 30% → Decrease count by 1
6. Saved configuration
   
<img src="https://imgur.com/TWLruR0.png" height="80%" width="80%" alt="Scale"/>

I then generated load against the app URL to trigger the scale-out rule, and monitored the **Run history** tab to confirm the scaling event fired, via using this command in bash.

<img src="https://imgur.com/hCjJERK.png" height="80%" width="80%" alt="Scale"/>

<img src="https://imgur.com/Goodp28.png" height="60%" width="80%" alt="Scale"/>

<img src="https://imgur.com/r8fZxld.png" height="60%" width="80%" alt="Scale"/>

---
 
## Key Takeaways
 
- Autoscale requires at minimum the Standard tier — Free and Shared tiers don't expose this setting
- Scale-out and scale-in rules are evaluated independently, so both need to be defined or the plan will only ever grow, never shrink
- Deployment method choice (Zip Deploy vs GitHub Actions vs Local Git) affects how repeatable future deployments are — GitHub Actions is best for ongoing CI/CD, Zip Deploy is fastest for one-off testing
- Run history under Autoscale settings is the best place to confirm rules are actually firing, rather than assuming from instance count alone

---
 
## Lessons Learned
 
- <b>Plan the tier first</b> — I learned the pricing tier has to be decided before Autoscale is even an option, so it's worth choosing Standard or above from the start rather than upgrading later mid-lab.
- <b>Test before trusting</b> — Watching the Run history after generating load helped confirm the rules were actually evaluating correctly, rather than assuming the configuration was correct just because it saved without errors.



*Lab completed by [@Nako8k](https://github.com/Nako8k)*
