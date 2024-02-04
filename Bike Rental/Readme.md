# Explore Automated Machine Learning in Azure Machine Learning

In this exercise, you'll use the automated machine learning feature in Azure Machine Learning to train and evaluate a machine learning model. You'll then deploy and test the trained model.

**Estimated Time:** Approximately 30 minutes

## Create an Azure Machine Learning workspace

To use Azure Machine Learning, you need to provision an Azure Machine Learning workspace in your Azure subscription. Follow these steps:

1. Sign into the [Azure portal](https://portal.azure.com) using your Microsoft credentials.
2. Select **+ Create a resource**, search for **Machine Learning**, and create a new Azure Machine Learning resource with the following settings:
   - **Subscription:** Your Azure subscription.
   - **Resource group:** Create or select a resource group.
   - **Name:** Enter a unique name for your workspace.
   - **Region:** Select the closest geographical region.
   - **Storage account:** Note the default new storage account.
   - **Key vault:** Note the default new key vault.
   - **Application insights:** Note the default new application insights resource.
   - **Container registry:** None (created automatically when deploying a model).

3. Select **Review + create**, then select **Create**. Wait for your workspace to be created, and then go to the deployed resource.

4. Select **Launch studio** or navigate to [Azure Machine Learning studio](https://ml.azure.com) in a new browser tab. Sign in using your Microsoft account. Close any displayed messages.

5. In Azure Machine Learning studio, you should see your newly created workspace. If not, select **All workspaces** in the left-hand menu and then choose the workspace you created.

## Use automated machine learning to train a model

Automated machine learning enables you to try multiple algorithms and parameters to train multiple models and identify the best one for your data. In this exercise, you'll use a dataset of historical bicycle rental details to predict the number of bicycle rentals on a given day.

**Citation:** The data used is derived from Capital Bikeshare and used under the published data license agreement.

1. In Azure Machine Learning studio, navigate to the **Automated ML** page (under Authoring).

2. Create a new Automated ML job with the following settings:

**Basic settings:**
- **Job name:** mslearn-bike-automl
- **New experiment name:** mslearn-bike-rental
- **Description:** Automated machine learning for bike rental prediction
- **Tags:** none

**Task type & data:**
- **Select task type:** Regression
- **Select dataset:** Create a new dataset named **bike-rentals** with the provided settings.

**Task settings:**
- **Task type:** Regression
- **Dataset:** bike-rentals
- **Target column:** Rentals (integer)
- **Primary metric:** Normalized root mean squared error
- **Explain best model:** Unselected
- **Use all supported models:** Unselected
- **Allowed models:** Select only RandomForest and LightGBM
- **Limits:** Set max trials, max concurrent trials, max nodes, metric score threshold, timeout, iteration timeout, and enable early termination as specified.

**Validation and test:**
- **Validation type:** Train-validation split
- **Percentage of validation data:** 10
- **Test dataset:** None

**Compute:**
- **Select compute type:** Serverless
- **Virtual machine type:** CPU
- **Virtual machine tier:** Dedicated
- **Virtual machine size:** Standard_DS3_V2*
- **Number of instances:** 1

*If your subscription restricts VM sizes, choose any available size.

3. Submit the training job. It starts automatically.

4. Wait for the job to finish. It might take a while—now might be a good time for a coffee break!

# Review the Best Model

Once the automated machine learning job has completed, you can review the best model it trained.

1. On the **Overview** tab of the automated machine learning job, note the best model summary.

![image](https://github.com/CassioD/AI-900/assets/87616806/0548fb67-fe3e-4e2e-a6ce-615ef75a5140)


   **Note:** You may see a message under the status "Warning: User specified exit score reached…". This is an expected message. Please continue to the next step.

2. Select the text under **Algorithm name** for the best model to view its details.

3. Go to the **Metrics** tab and select the residuals and predicted_true charts if they are not already selected.

Review the charts showing the performance of the model. The residuals chart displays the differences between predicted and actual values as a histogram. The predicted_true chart compares the predicted values against the true values.

## Deploy and Test the Model

On the **Model** tab for the best model trained by your automated machine learning job, select **Deploy** and use the **Web service** option with the following settings:

- **Name:** predict-rentals
- **Description:** Predict cycle rentals
- **Compute type:** Azure Container Instance
- **Enable authentication:** Selected

Wait for the deployment to start - this may take a few seconds. The **Deploy status** for the `predict-rentals` endpoint will be indicated in the main part of the page as Running.

Wait for the **Deploy status** to change to Succeeded. This may take 5-10 minutes.

## Test the Deployed Service

Now you can test your deployed service.

1. In Azure Machine Learning studio, on the left-hand menu, select **Endpoints** and open the `predict-rentals` real-time endpoint.

2. On the `predict-rentals` real-time endpoint page, view the **Test** tab.

3. In the **Input data to test endpoint** pane, replace the template JSON with the following input data:

   ```json
   {
     "Inputs": { 
       "data": [
         {
           "day": 1,
           "mnth": 1,   
           "year": 2022,
           "season": 2,
           "holiday": 0,
           "weekday": 1,
           "workingday": 1,
           "weathersit": 2, 
           "temp": 0.3, 
           "atemp": 0.3,
           "hum": 0.3,
           "windspeed": 0.3 
         }
       ]    
     },   
     "GlobalParameters": 1.0
   }

   
Click the Test button.

Review the test results, which include a predicted number of rentals based on the input features.

```json
{
  "Results": [
    444.27799000000000
  ]
}
```
The test pane took the input data and used the model you trained to return the predicted number of rentals.

Let’s review what you have done. You used a dataset of historical bicycle rental data to train a model. The model predicts the number of bicycle rentals expected on a given day, based on seasonal and meteorological features.

# Clean-up
The web service you created is hosted in an Azure Container Instance. If you don’t intend to experiment with it further, you should delete the endpoint to avoid accruing unnecessary Azure usage.

In Azure Machine Learning studio, on the Endpoints tab, select the predict-rentals endpoint. Then select Delete and confirm that you want to delete the endpoint.

Deleting your compute ensures your subscription won’t be charged for compute resources. You will however be charged a small amount for data storage as long as the Azure Machine Learning workspace exists in your subscription. If you have finished exploring Azure Machine Learning, you can delete the Azure Machine Learning workspace and associated resources.

To delete your workspace:

In the Azure portal, in the Resource groups page, open the resource group you specified when creating your Azure Machine Learning workspace.
Click Delete resource group, type the resource group name to confirm you want to delete it, and select Delete.
