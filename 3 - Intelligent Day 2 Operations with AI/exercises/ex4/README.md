# Exercise 4 – Trigger a Remediation Command in SAP Automation Pilot Directly from SAP Cloud ALM

In this exercise, you will:
- Consume an alert in **SAP Cloud ALM – Health Monitoring**
- Trigger a remediation command in **SAP Automation Pilot** directly from Cloud ALM
- Validate the result and **close the alert** in Health Monitoring

> **Context:**  
> In the previous exercise, you derived AI-driven insights indicating high memory utilization and a recommendation to upscale. Here, you’ll execute that recommendation by triggering a remediation command from **SAP Cloud ALM** into **SAP Automation Pilot**.

For a better understanding of the use case, refer to the diagram below:  
<img src="./images/ex-04-scenario.png" width="700" height="400">

---

## Exercise 4.1 – Consume an Alert in SAP Cloud ALM and Trigger a Remediation Command

In Exercise 3 you identified that your application’s memory utilization reached the Cloud Foundry limit and the AI suggested **upscaling memory**. You’ll now trigger an Automation Pilot command **from Cloud ALM** to remediate.

1. Access **SAP Cloud ALM**:  
   [https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/](https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/)

2. **Log in** → go to **Operations** → **Health Monitoring**.

3. Ensure the **Scope** includes your subaccount (e.g., `XP267-0XX_CF`).  

4. Open the **Alerts** view from the left sidebar.  
   ![](./images/04-01.png)

5. You should see at least one active alert, e.g., **High Memory Utilization (node.js)**. **Click** the alert.  
   ![](./images/04-02.png)

6. In the alert details, click **Actions** to view available remediations.  
   ![](./images/04-03.png)

7. Select **Start Operation Flow**.  
   ![](./images/04-04.png)

8. Click **Register Operation Flow** and choose **SAP Automation Pilot**.  
   > *Note:* This is a **one-time registration** per command you want to trigger from Cloud ALM.  
   ![](./images/04-05.png)

9. In **Register SAP Automation Pilot**, fill in:
   - **Endpoint**: `AP-XP267-XXX`  
     *(Example: for user `XP267_001`, endpoint is `AP-XP267-001`. This is the endpoint you created in Exercise 1 within **Landscape Management**.)*
   - Click the **ID** field’s lookup icon to select a command.  
     ![](./images/04-06.png)

10. From **Select SAP Automation Pilot**, choose the command:
    - `UpscaleAppXP267UserXXX`  
      *(Example: for user `XP267_001`, pick `UpscaleAppXP267User001`.)*  
    - Click the command, then **OK**.  
      ![](./images/04-07.png)  
      ![](./images/04-08.png)

11. The remediation command is now registered. Click **Start** to **trigger** it.  
    ![](./images/04-09.png)

---

## Exercise 4.2 – Validate Execution and Review Outputs

1. In the **Alert** details, open the **Operation Flow** tab.  
   ![](./images/04-10.png)

2. You’ll see the SAP Automation Pilot command instance and its status. Click the **Instance ID** to open it directly in Automation Pilot.  
   ![](./images/04-11.png)

3. In the popup, view the command completion screen. Click **Show** under **Output** to review the results.  
   ![](./images/04-12.png)

4. Validate that the application was restarted and the state is **RUNNING**. This indicates the memory upscale action executed successfully.  
   ![](./images/04-13.png)

---

## Exercise 4.3 – Confirm the Upscale in SAP BTP Cockpit

1. In your BTP subaccount, navigate to the Cloud Foundry **dev** space.

2. Open the application **`bookshop-srv`** to verify:
   - **Memory per Instance** increased (e.g., from **400 MB** to **656 MB**).  
   - In **Most Recent Application Events**, find the scaling event: `audit.app.process.scale`.  
     ![](./images/04-14.png)

✅ **Well done!** The memory allocated to your CAP app has been increased.

> **Hint:** In the Automation Pilot command `UpscaleAppXP267UserXXX`, open the executor **`setAppResources`** and inspect parameter **`memory`**. You’ll see:  
> `$(.getAppState.output.memory + 256)`  
> This adds **256 MB** to the current memory. You can modify this value, expose it as a dynamic input, or set it manually at trigger time.  
> ![](./images/04-15.png)

---

## Exercise 4.4 – Close the Alert in SAP Cloud ALM

1. Return to **SAP Cloud ALM – Health Monitoring** and open the **Alerts** → **Alert details** page.

2. Click **Actions** → **Confirm** to close the alert after validating the fix.  
   ![](./images/04-16.png)

🎉 **Success!** You remediated the issue via **SAP Automation Pilot** and closed the alert in **SAP Cloud ALM**.

---

## Summary

You have learned how to:
- Register Automation Pilot commands as **operation flows** in SAP Cloud ALM  
- **Trigger** remediation actions directly from an alert  
- Validate outcomes in **Automation Pilot** and **SAP BTP Cockpit**  
- **Close** the alert once remediation is verified

This completes the hands-on flow from **observability → AI-driven insight → automated remediation → confirmation**.
