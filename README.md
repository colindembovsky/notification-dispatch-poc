# Notification Dispatch POC

This repo was created to demonstrate how to create Azure Boards Work Items for GitHub Advanced Security notifications from Code Scanning and/or Dependabot

## Architecture

The solution uses Azure Pipelines Incoming WebHook service to trigger a pipeline from `POST`s sent by webhooks triggered by GitHub events. The Azure Pipeline will then create a WorkItem with the details of the notification.

## Setup

### 1. Create a `Incoming WebHook Service Connection` on your Azure DevOps project

1. Go to **Project Settings > Service connections > New service connection > Incoming WebHook**
2. On **WebHook Name**, use `gitHubWebHook` (this name will be used in the URL that you `POST` to)
3. Add a secret to it (use a random string, but remember it for later)
4. On **Http Header**, use `X-Hub-Signature`
5. On **Service connection name**, use `GitHubWebHook` (this name will be used inside the Azure Pipeline)

### 2. Create a Webhook on GitHub

1. In your repository go to **Settings > Webhooks > Add webhook**
2. On **Payload URL**, fill in with your Service Connection URL created on Azure DevOps, use the following pattern 

```
https://dev.azure.com/<ADO Organization>/_apis/public/distributedtask/webhooks/gitHubWebHook?api-version=6.0-preview
```

3. On **Content type**, select `application/json`
4. On **Secret**, fill in the same secret used in **1.3**
5. Select "**Let me select individual events**"
6. Then check one or more of the following options
   1. **Code scanning alerts**
   2. **Repository vulnerability alerts**
   3. **Secret scanning alerts**
   * Remember to uncheck **Pushes**
7. Click **Add webhook**

Well done, you have now set the webhook trigger from GitHub and the receiving endpoint on Azure DevOps. The next step is to create the Azure Pipeline which will receive the payload and create the WorkItem.

### 3. Create a new Pipeline on Azure DevOps

1. In your Azure DevOps project got to **Pipelines > New pipeline**
2. Select GitHub
3. Select the repo that contains [this file](https://github.com/colinsalmcorner/notification-dispatch-poc/blob/main/pipelines/webhook.yml)
4. Browse to the file
6. Click **Save**

Voilà!

### Customizing the Pipeline

The pipeline "hard-codes" the Team Project to the Team Project of the Pipeline itself. It also defaults Area Path and Iteration Path to `/`. The payload is extracted using inline PowerShell - you can update the title and description if you choose to, or even add logic to determine which Project, Area Path and Iteration Path to set for the WorkItem.
