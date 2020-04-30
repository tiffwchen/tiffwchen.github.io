---
layout: post
title: Automating Azure WebJob Deployments
excerpt: Deploy Azure WebJobs (Azure Portal and Azure DevOps)
---

## _Deploying Azure WebJobs Through Azure Portal and Azure DevOps_

### What is a WebJob?

A WebJob is a program or script that is run in the same context as an application hosted within an Azure App Service. WebJobs are either continuous or triggered. Continuous WebJobs run immediately when created and run on all instances that the web app runs on unless otherwise restricted by the user. In contrast, triggered WebJobs run when triggered via schedule or manually, and run on a single instance selected by Azure. 

Values in an Azure App Service’s application settings can be used within the WebJob like this: “$env:NAME_OF_VARIABLE”.

Note: as of 4/25/2020, WebJobs are not available for Linux App Service instances.

### Required Resources 

* Azure subscription 
* Azure Resources:
    * Azure Resource Group
    * Azure App Service
* Azure DevOps Account 
* WebJob script

### Manual Deployment Instructions

For detailed manual deployment instructions with screenshots, see Microsoft documentation for [webjob creation](https://docs.microsoft.com/en-us/azure/app-service/webjobs-create).

Package the WebJob in a .zip file and within WebJobs blade of the App Service, upload the .zip file. Then, select the type of WebJob (either “Continuous or “Triggered”) and the scale (“Multi Instance” or “Single Instance”). 

### Automated Deployment Instructions 

Deploy an App Service resource within Azure. Whip up a default WebJob script and place it within your Azure DevOps repository or find one on the internet. 

#### Build Pipeline 

Create a new build pipeline. Within the build pipeline, add two tasks, “Copy files” and “Publish build artifacts”. For “Copy Files,” under the “Target Folder” input, input a path that begins with `“$(Build.ArtifactStagingDirectory)\MAIN_TARGET_FOLDER”`. For this example, I named my MAIN_TARGET_FOLDER, “web_job,” for simplicity. For continuous WebJobs – the rest of the file path should be `“app_data\jobs\continuous\JOB_NAME"` – and for triggered WebJobs the rest of the file path should be `“app_data\jobs\triggered\JOB_NAME”`. For this example, I am deploying a triggered WebJob name “testwebjob” so my full file path for the target folder is: “$(Build.ArtifactStagingDirectory)\web_job\app_data\jobs\triggered\testwebjob”

![Azure Build Pipeline - Copy Files Task](/images/azurewebjobdeployment/BuildPipelineCopyTask.png)
<!-- .element height="200%" width="200%" -->

For the “Publish build artifacts” task, all defaults can be left as is.

![Azure Build Pipeline - Publish Build Artifacts](/images/azurewebjobdeployment/BuildPipelinePublishArtifact.png)

Run your build pipeline, and make sure it succeeds before you move on to the release pipeline. 

#### Release Pipeline

Create a new release pipeline. Within that release pipeline, add an “Azure App Service deploy” task. Fill in the “Azure subscription,” “App Service type,” and “App Service name” fields from the selections within the dropdown menus. For the “Package or folder” field, fill in the target folder file path you created in the Build pipeline up to the “app_data” directory (but don’t include it). For example, my target folder file path for where my webjob artifact was published was “$(Build.ArtifactStagingDirectory)\web_job\app_data\jobs\triggered\testwebjob”, so I would fill the “Package or folder” field with `“$(System.DefaultWorkingDirectory)/**/web_job”`. The `‘**’` in the file path is a recursive wildcard for matching the file path of the desired “web_job” directory no matter how deeply it’s nested.

![Azure Release Pipeline - Publish Build Artifacts](/images/azurewebjobdeployment/ReleasePipeline.png)

Run the release, and if it’s successful, you should be able to view your newly deployed WebJob under the “WebJobs” tab in your App Service. 

### Automating Deployment of Triggered WebJob with Schedule

![Azure WebJob Unscheduled](/images/azurewebjobdeployment/WebJobScheduleNA.png)

You might notice at this point that under the Schedule column for your WebJob, the text says “n/a.” This means that the WebJob can only be manually triggered. If you want to have a scheduled time trigger for your WebJob, you need to include a file named “settings.job” within your WebJob directory. Within the file, you add an object with key “schedule” and the value set as a CRON expression.

![settings.job](/images/azurewebjobdeployment/scheduledotjob.png)

Save your file, push your code changes, run the build and release pipelines again, and if successful, the CRON expression value will replace the “n/a” under the “Schedule” column for the WebJob. 

![Azure WebJob Scheduled](/images/azurewebjobdeployment/WebJobScheduleSet.png)

Please refer to this [blog post](https://arminreiter.com/2017/02/azure-functions-time-trigger-cron-cheat-sheet/) from Armin Reiter for advice on how to formulate your desired CRON expression. 

### Sources
-	https://docs.microsoft.com/en-us/azure/app-service/webjobs-create
-	https://arminreiter.com/2017/02/azure-functions-time-trigger-cron-cheat-sheet/
-	https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/file-matching-patterns?view=azure-devops
