# Exercise 1 – Access SAP Cloud ALM and Trigger Commands to Your App via SAP Automation Pilot

In this exercise, you will:  
- Access **SAP Cloud ALM** and explore metrics in **Health Monitoring**  
- Interact with application endpoints via **SAP Automation Pilot**  
- Set up the integration between **SAP Cloud ALM** and **SAP Automation Pilot**

For a better understanding of the use case, refer to the diagram below:  
<img src="./images/ex-01-scenario.png" width="700" height="400">

---

## Exercise 1.1 – Access SAP Cloud ALM and Explore Health Monitoring Metrics

1. Access **SAP Cloud ALM**:  
   [https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/](https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/)

   **Note**: click on the option to sign in with: `tdct3ched1.accounts.ondemand.com` and then log in with the credentials (email and password you had already used in exercise 0) 
   ![](./images/1-00-00-1.png)

2. **Log in** with your provided user credentials.  

3. From the main menu, select **Operations**.  
   ![](./images/01-01.png)

4. Click on **Health Monitoring**.  
   ![](./images/01-02.png)

5. You will land on the **Health Monitoring Overview** screen.  
   Make sure the **Scope** is set to your subaccount, e.g. `XP267-0XX_CF` (for example, `XP267_001_CF`).  
   ![](./images/01-03.png)

6. Click on the tile to view all available services and systems in Health Monitoring.  
   ![](./images/01-04.png)

7. Click on a service to access the **SAP BTP Cloud Foundry Metrics Overview**.  
   These are metrics ingested from your CAP app through **OpenTelemetry**.  
   ![](./images/01-05.png)

8. Optionally, click on the **All Metrics** tab to explore the collected metrics in detail.  
   ![](./images/01-06.png)

✅ **Well done!** You can now monitor the health of your CAP app.  
Next, you will explore **SAP Automation Pilot** and interact with your app endpoints.

---

## Exercise 1.2 – Interact with App Endpoints via SAP Automation Pilot

### Exercise 1.2.1 – Explore Main Sections in SAP Automation Pilot

1. Return to your **SAP BTP subaccount** (`XP267-0XX`, for example, `XP267-001`).  

2. From the left-side menu, go to **Services → Instances and Subscriptions**.  

3. Under **Subscriptions**, click on **Automation Pilot**.  
   ![](./images/01-1-01.png)

4. You will land on the **SAP Automation Pilot Dashboard**.  
   ![](./images/01-1-02.png)

5. From the left menu, open **Provided Catalogs**.  
   Browse through the 300+ ready-to-use automation commands grouped by catalogs.  
   ![](./images/01-1-03.png)

You can also explore:  
- **My Catalogs** – your custom and extended commands  
- **Commands** – all commands available to your user  
- **Inputs** – reusable parameters that can be referenced in commands  

---

### Exercise 1.2.2 – Build Your First Command in SAP Automation Pilot

Let’s create your first custom command.

1. Click  the **Commands** menu item from the left sidebar to navigate to the "Commands" section in SAP Automation Pilot 
   ![](./images/1-00-00-2.png)

2. Click **Create**.  
   ![](./images/1-00-00-3.png)

3. Fill in the details:  
   - **Catalog**: type in `XP267 Ex01 – Kick Start Commands` and from the drop-down select your XP267 Kick Start Catalog
   - **Name**: `simpleHttpRequest`  

   Click **Create**.  
   ![](./images/1-00-00-4.png)

4. The command is created but currently has no inputs, outputs, or executors.  
   ![](./images/1-2-2-04.png)

#### Add Inputs

1. Click **Add** under the **Input Keys** section.  
   ![](./images/1-2-2-05.png)

   Fill in:  
   - **Name**: `url`  
   - **Type**: `string`  
   - Mark as **Required**  
   Click **Add**.  
   ![](./images/1-2-2-06.png)

2. Add another input key to define the HTTP method:  
   - **Name**: `method`  
   - **Type**: `string`  
   - **Default Value Source**: `Static`  
   - **Value**: `GET`  
   Click **Add**.  
   ![](./images/1-2-2-07.png)

#### Add Executor

1. Click **Add** under the **Executors** section.  
   ![](./images/1-2-2-08.png)

2. In the **Add Executor** dialog:  
   - Click **Here** to define the execution order  
   - **Alias**: `getAppEndpoint`  
   - **Command**: `HttpRequest`  
   - Keep **Automap parameters** enabled  
   Click **Add**.  
   ![](./images/1-2-2-09.png)

   You will now see the executor added with mapped parameters.  
   ![](./images/1-2-2-10.png)

#### Add Outputs

1. Click **Add** under the **Output Keys** section.  
   ![](./images/1-2-2-11.png)

   Add the following outputs:  
   - **Name**: `status` – **Type**: `number`  
   - **Name**: `body` – **Type**: `string`  
   ![](./images/1-2-2-14.png)

3. Scroll down to **Configuration**, open the **output** executor, click **Edit**, and map the outputs to their respective values:  
   - **body**: `$(.getAppEndpoint.output.body)`    
   - **status**: `$(.getAppEndpoint.output.status)`  
   Click **Update**.  
   ![](./images/1-00-00-5.png)
   ![](./images/1-2-2-16-2.png)


#### Trigger the Command

1. Click **Trigger**.  
   ![](./images/1-2-2-15.png)

2. Provide the **URL** of your Bookshop web app (copied from Exercise 0) and click **Trigger**.  
   ![](./images/1-2-2-16.png)

3. Once complete, click **Show** under **Output** to view the returned values.  
   ![](./images/1-2-2-17.png)

✅ The command executed successfully and returned outputs such as status, headers, and response body.
![](./images/01-00.png)

---

### [ 🧩 Optional ] – Using Stored Input Keys
> **Note (Optional Exercise):**  
> This exercise is **optional**. Due to time limitations, it is **recommended to proceed with the next tasks** first.  
> You can return to the optional exercises **after completing the full scenario**, if time permits.


You can store and reuse input values in **SAP Automation Pilot** so that you are not asked to provide static parameters each time a command gets triggered but the input can be fetched dynamically or from already stored input key.

Let's demonstrate how that works by extending the command you just have created - `simpleHttpRequest` . First , let's start by exploring and updating an input key. 

1. Click **Inputs** in the left menu.  
   ![](./images/1-2-2-21.png)

2. Select the input set `appEndPoints` and click the **Edit** icon.  
   ![](./images/1-2-2-22.png)

3. Update the `urlHomepage` value with your app’s URL and **Save**.  
   ![](./images/1-2-2-23.png)

4. Click on **My Catalogs** menu item from the left sidebar, then click on **Kick Start Catalog** and open the `simpleHttpRequest` command.  
   ![](./images/new-6.png)
   ![](./images/new-7.png)

6. Edit the **URL** input key within the **Input Keys contract section** by clicking on the **pencil button** to reference the stored value:  
   - Uncheck **Required**  
   - **Default Value Source**: `Input Key`  
   - **Input**: `appEndPoints`  
   - **Input Key**: `urlHomepage`  
   Click **Update**.
   ![](./images/01-01-extra.png)
   ![](./images/1-2-2-25.png)

7. Trigger the command again — it will now automatically use the stored input value.  
   ![](./images/1-2-2-26.png)

✅ You’ve learned how to create, execute, and reuse inputs for commands in SAP Automation Pilot.

---

## Exercise 1.3 – Set Up Integration Between SAP Cloud ALM and SAP Automation Pilot

Now, let’s integrate **SAP Cloud ALM** with **SAP Automation Pilot** to to enable automated command execution.

### Step 1 – Gather Required Values from SAP Automation Pilot

In **SAP Automation Pilot**, click on the **User** menu (bottom left corner) and copy the following:  
- Tenant ID  
- Tenant URL  
![](./images/01-2-01.png)

Then click **API** (bottom left menu) and copy the **Base URL**.  
![](./images/01-2-02.png)

### Step 2 – Create a Service Account

1. In the left menu, go to **Service Accounts** → **Create**.  
   ![](./images/01-2-03.png)

2. Fill in:  
   - **Username**: `cloudALM`  
   - **Description**: e.g. *Service account used in SAP Cloud ALM to trigger commands in SAP Automation Pilot*  
   - **Permissions**: `Read`, `Write`, `Execute`  
   - **Authentication Type**: `Basic`  

   Click **Create**.  
   ![](./images/01-2-04.png)

4. Copy the **Username** and **Password** immediately — the password will not be displayed again.  
   ![](./images/01-2-05.png)

Click **Close** when you are done.

You should now have the following values ready:  
- Tenant ID (e.g. `1T0011140RX`)   
- Tenant URL (e.g. `https://xp267-0XX-1a0od9aa.autopilot.cfapps.eu10.hana.ondemand.com`)
- Base URL (e.g. `https://emea.autopilot.cloud.sap`)
- Username (already copied)
- Password (already copied)

---

### Step 3 – Configure the Connection in SAP Cloud ALM

1. Open **SAP Cloud ALM**:  
   [https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/](https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/)

2. From the main menu, go to **Operations → Landscape Management**.  
   ![](./images/01-2-06.png)

3. From the left sidebar, select **Services and Systems** → click **Add New Service**.  
   ![](./images/01-2-07.png)

4. Fill in the **Add Service** form:  
   - **Name**: `AP-XP267-0XX` (e.g. `AP-XP267-001` for User 01 , `AP-XP267-041` for User 41, etc.)
   - **System Number**: *Tenant ID* (copied earlier, e.g. `1T0011140RX` )  
   - **Service Type**: `SAP Automation Pilot`  
   - **Role**: `Test`  
   - **Root URL**: *Tenant URL*  (e.g. `https://xp267-0XX-1a0od9aa.autopilot.cfapps.eu10.hana.ondemand.com`) 
   - **Deployment Model**: `BTP System`  

   Click **Save**.  
   ![](./images/01-03-extra.png)

5. Select the newly created service and open its details.  
   ![](./images/01-2-09.png)

6. Go to the **Endpoints** tab → click **Add**.  
   ![](./images/01-2-10.png)

7. Fill in the endpoint details:  
   - **Endpoint Name**: `AP-XP267-0XX` (e.g. `AP-XP267-001` for User 01 , `AP-XP267-041` for User 41, etc.) 
   - **Root URL**: *Base URL*  (e.g. `https://emea.autopilot.cloud.sap`)
   - **Authentication**: Basic  
   - **User**: *Username*  
   - **Password**: *Password*  

> ⚠️ **Remark:**  
> **Do not** click the **Check Connection** button yet — the service has not been provisioned, and the connection check will fail until it is in place.


   Click **Save**.  
   ![](./images/01-2-11.png)

9. After saving, click **Ping Connection** to verify the setup.  
   ![](./images/01-2-12.png)

✅ **Success!** The endpoint connection is active. You can now trigger Automation Pilot commands directly from **SAP Cloud ALM**.  
   ![](./images/01-2-13.png)

---

## Summary

You have successfully:  
- Explored **SAP Cloud ALM Health Monitoring**  
- Created and executed commands in **SAP Automation Pilot**  
- Integrated both products for automated operational actions  

Proceed to the next step:  
➡️ [Exercise 2 – Extend SAP Automation Pilot with SAP AI Core for Log Assessment and AI Recommendations](../ex2/README.md)
