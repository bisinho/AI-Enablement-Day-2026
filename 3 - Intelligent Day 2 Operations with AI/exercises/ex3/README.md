# Exercise 3 – Extend SAP Automation Pilot with SAP AI Core for Log Assessment and AI Recommendations

In this exercise, you will:
- Run and extend a command in **SAP Automation Pilot** to collect application logs and **Cloud Foundry environment details**  
- Integrate SAP Automation Pilot with **SAP AI Core** to provide logs and context to an AI model for assessment and recommendations  
- Retrieve a summarized assessment in Automation Pilot and **push AI-powered insights to SAP Cloud ALM – Health Monitoring**

> **Context:**  
> In **Exercise 2**, you pushed custom usage metrics to **SAP Cloud ALM** . 
> Here, you will enrich your observability with **AI-generated assessments** based on real application logs and environment context.

For a better understanding of the use case, refer to the diagram below:  
<img src="./images/ex-03-scenario.png" width="700" height="400">

---

## Exercise 3.1 – Collect App Logs and State Using a Command in SAP Automation Pilot

1. In **SAP Automation Pilot**, navigate to **My Catalogs → Commands** under the catalog:  
   `XP267 Ex03 – AI Powered Commands`  
   <img src="./images/03-01.png" width="700" height="400">

2. Open the command **`AppLogsAnalysisByAI`**.  
   ![](./images/03-02.png)

   This command is preconfigured to collect logs, current state, and the latest events for your CAP application.

3. Click **Trigger** to execute (no input required).  
   ![](./images/03-03.png)

4. After successful execution, click **Show** under **Output** to review the collected data.  
   ![](./images/03-04.png)

   You now have a snapshot of CAP app logs, state, and events.  
   ![](./images/03-05.png)

### Extend the Command by Adding an Output Key for State Memory

1. In the **Output Keys** section, click **Add**.  
   ![](./images/03-06.png)

   Fill in:  
   - **Name**: `stateMemory`  
   - **Type**: `number`  

   Click **Add**.  
   ![](./images/03-07.png)

2. Scroll down to **Configuration**, open the **output** executor, click **Edit**, and map the value:  
   - **stateMemory**: `$(.GetAppState.output.memory)`  

   Click **Update**.  
   ![](./images/03-08.png)  
   ![](./images/03-09.png)

3. **Trigger** the command again and review the **Output**.  
   You should see the memory allocated for the space where your CAP app runs (e.g., **400 MB**).  
   ![](./images/03-10.png)

---

## Exercise 3.2 – Integrate SAP Automation Pilot with SAP AI Core for AI Assessment & Recommendations

Now that you collect the relevant data, let’s add an AI-based assessment using **SAP AI Core**.

> **Note:** An SAP AI Core service and model deployment are already prepared.  
> The **Service Key** is stored in your Automation Pilot tenant.

1. Open the command **`AppLogsAnalysisByAI`**, scroll to **Executors**, and click **Add**.

2. Configure the executor:  
   - **Placement**: just before **Output** (click **Here**)  
   - **Alias**: `AICoreAnalyze`  
   - **Command**: `GenerateGpt4OmniCompletion` (sends data to AI Core and returns a response)  
   - Keep **Automap parameters** enabled  
   - Click **Add**  
   ![](./images/03-11.png)

3. Edit **`AICoreAnalyze`** and set:  
   ![](./images/03-12.png)

   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**: `App logs are $(.GetAppLogs.output.logs) , have in mind also the memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB"`  
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive application logs and the allocated memory for a CAP NodeJS application running on BTP Cloud Foundry. Your task is to analyze these logs and provide a summary highlighting the application's current state and any potential issues.
     ```

   Click **Update**.  
   ![](./images/03-13.png)  
   ![](./images/03-14.png)

### Expose the AI Assessment as an Output

1. In **Output Keys**, click **Add**.  
   ![](./images/03-16.png)

   - **Name**: `AIOutput`  
   - **Type**: `string`  

   Click **Add**.  
   ![](./images/03-17.png)

2. Map the output in the **output** executor (**Edit**):  
   - **AIOutput**: `$(.AICoreAnalyze.output.response)`  

   Click **Update**.  
   ![](./images/03-18.png)  
   ![](./images/03-19.png)

3. **Trigger** the command. After completion, click **Show** under **Output** to read the AI assessment.  
   ![](./images/03-15.png)  
   ![](./images/03-20.png)  
   ![](./images/03-21.png)

---

## [ 🧩 Optional ] Exercise 3.3 – Generate a One-Word Health Status Summary via SAP AI Core

> **Note (Optional Exercise):**  
> This exercise is **optional**. Due to time limitations, it is **recommended to proceed with the next tasks** first.  
> You can return to the optional exercises **after completing the full scenario**, if time permits.

Let’s obtain a concise **single-status** health summary (`OK`, `Investigate`, or `Critical`) suitable for dashboards.

1. In **`AppLogsAnalysisByAI`**, add another executor:  
   - **Placement**: before **Output**  
   - **Alias**: `AICoreAnalyzeSummary`  
   - **Command**: `GenerateGpt4OmniCompletion`  
   - Keep **Automap parameters** enabled  
   - Click **Add**    
   ![](./images/03-24.png)

2. Edit **`AICoreAnalyzeSummary`** (note: ensure you edit the *Summary* executor):  
   ![](./images/03-25.png)

   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**: `$(.AICoreAnalyze.output.response)`  _(reuse the previous AI result)_  
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive an analysis of the logs from a CAP NodeJS application running on Cloud Foundry space. Your task is to analyze further the inputs and provide your assessment about the app health in just one status (use one out of these statuses as a single word without any formatting: OK, Investigate, Critical).
     ```

   Click **Update**.  
   ![](./images/03-26.png)  
   ![](./images/03-27.png)

3. Add a new **Output Key**:  
   ![](./images/03-28.png)

   - **Name**: `AIOutputSummary`  
   - **Type**: `string`  

   Click **Add**.  
   ![](./images/03-29.png)

4. Map the summary in the **output** executor (**Edit**):  
   - **AIOutputSummary**: `$(.AICoreAnalyzeSummary.output.response)`  

   Click **Update**.  
   ![](./images/03-30.png)  
   ![](./images/03-31.png)

5. **Trigger** the command and check **Output**. You should see `AIOutputSummary` with a one-word status (e.g., `Investigate`).  
   ![](./images/03-32.png)  
   ![](./images/03-33.png)  
   ![](./images/03-34.png)

---

## [ 🧩 Optional ] Exercise 3.4 – Add AI Insights Focused on Memory Consumption

> **Note (Optional Exercise):**  
> This exercise is **optional**. Due to time limitations, it is **recommended to proceed with the next tasks** first.  
> You can return to the optional exercises **after completing the full scenario**, if time permits.

Add an AI analysis specialized in **memory utilization**, plus a **short summary**.

1. Add an executor:  
   - **Alias**: `AICoreMemoryAnalysis`  
   - **Command**: `GenerateGpt4OmniCompletion`  
   - **Placement**: before **Output**  
   - Keep **Automap parameters** enabled  
   - Click **Add**  
   ![](./images/03-35.png)

2. Edit **`AICoreMemoryAnalysis`** and set:  
   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**:  
     ```
     Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)
     ```
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive as inputs the available memory allocated within the Cloud Foundry space where the app runs and the logs from the same CAP NodeJS application. Analyze these inputs and provide an assessment of the app's memory utilization with recommendations focused only on memory utilization.
     ```

   Click **Update**.

3. Add another executor for a **very short memory summary** (≤ 3 words):  
   - **Alias**: `AICoreMemoryAnalyzeSummary`  
   - **Command**: `GenerateGpt4OmniCompletion`  
   - **Placement**: before **Output**  
   - Keep **Automap parameters** enabled  
   - Click **Add**  
   ![](./images/03-36.png)

4. Edit **`AICoreMemoryAnalyzeSummary`** and set:  
   ![](./images/03-25.png)

   - **deploymentId**: `$(.AICoreData.deploymentId)`  
   - **prompt**:  
     ```
     Available memory allocated within the Cloud Foundry space = "$(.GetAppState.output.memory) MB" and App logs are $(.AICoreAnalyze.output.response)
     ```
   - **serviceKey**: `$(.AICoreData.serviceKey)`  
   - **systemMessage**:  
     ```
     You are an AI assistant with expertise in CAP NodeJS applications. You will receive the available memory for the Cloud Foundry space and the app logs. Analyze and provide an assessment of the app's memory utilization summarized in up to 3 words.
     ```

   Click **Update**.

5. Add **Output Keys** and map them:  
   ![](./images/03-38.png)

   - **AIOutputMemory** (`string`) → `$(.AICoreMemoryAnalysis.output.response)`  
   - **AIOutputMemorySummary** (`string`) → `$(.AICoreMemoryAnalyzeSummary.output.response)`  

   Save the mappings in the **output** executor.  
   ![](./images/03-39.png)  
   ![](./images/03-40.png)

6. **Trigger** the command and check **Output** for both memory fields.  
   ![](./images/03-41.png)  
   ![](./images/03-42.png)  
   ![](./images/03-43.png)

---

## Exercise 3.5 – Push AI Insights to SAP Cloud ALM – Health Monitoring

To push the AI results to **SAP Cloud ALM**, use the extended command:

1. Go to **My Catalogs** → `XP267 Ex03 – AI Powered Commands` → **Commands**.  
   ![](./images/03-44.png)

2. Open **`AppLogsAnalysisByAIExtendedPushToCloudALM`**.  
   ![](./images/03-45.png)

   This version includes two additional executors:  
   - **`pushAppSummary`** – sends the **AI health status** (`AICoreAnalyzeSummary`) to Cloud ALM  
     ![](./images/03-46.png)
   - **`pushMemorySummary`** – sends the **memory summary** (`AICoreMemoryAnalyzeSummary`) to Cloud ALM  
     ![](./images/03-47.png)

3. **Trigger** the command. After completion, verify outputs and success state.  
   ![](./images/03-49.png)

---

## Exercise 3.6 – View AI Metrics in SAP Cloud ALM – Health Monitoring

1. Access **SAP Cloud ALM**:  
   [https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/](https://xp267-calm-1hdji9xc.eu10-004.alm.cloud.sap/)

2. Log in → **Operations → Health Monitoring**.

3. Ensure **Scope** includes your subaccount (e.g., `XP267-0XX_CF`) and open your **Cloud Foundry** environment.  
   ![](./images/03-48.png)

4. In **Metrics Overview**, under **Other Metrics**, find the new AI-driven metrics:  
   - `app.memory.ai`  
   - `app.status.ai`  

   These are the insights you just pushed from SAP Automation Pilot.  
   ![](./images/03-50.png)

---

## Summary

You have successfully:
- Collected app logs and state in **SAP Automation Pilot**  
- Integrated with **SAP AI Core** to generate assessments and recommendations  
- Produced a one-word **health status** and a concise **memory summary**  
- Pushed AI insights to **SAP Cloud ALM – Health Monitoring** for centralized observability

Proceed to the next step:  
➡️ [Exercise 4 – Trigger a Remediation Command in SAP Automation Pilot Directly from SAP Cloud ALM](../ex4/README.md)
