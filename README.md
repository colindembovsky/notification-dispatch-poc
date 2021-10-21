# Notification Dispatch POC

This repo was created for a POC on how to create Azure Boards Work Items for each vulnerability found by GitHub Advanced Security by Code Scanning and/or Dependabot

## How it works

It uses Azure Pipelines Incoming WebHook service to trigger a pipeline every time a webhook is triggered from GitHub. The pipeline will then create an WorkItem with the details of each vulnerability.

## Setup

### 1. Create a `Incoming WebHook Service Connection` on your Azure DevOps project

1. Go to **Project Settings > Service connections > New service connection > Incoming WebHook**
2. On **WebHook Name**, use `gitHubWebHook`
3. Add a secret to it (use a random generated string)
4. On **Http Header**, use `X-Hub-Signature`
5. On **Service connection name**, use `GitHubWebHook`

### 2. Create a Webhook on GitHub

1. In your repository go to **Settings > Webhooks > Add webhook**
2. On **Payload URL**, fill in with your Service Connection URL created on Azure DevOps, use the following pattern 

```
https://dev.azure.com/<ADO Organization>/_apis/public/distributedtask/webhooks/gitHubWebHook?api-version=6.0-preview
```

3. On **Content type**, select `application/json`
4. On **Secret**, fill in the same secret used in **1.3**
5. Select "**Let me select individual events**"
6. Then check the following options
   1. **Code scanning alerts**
   2. **Repository vulnerability alerts**
   3. **Secret scanning alerts**
   * Remember to uncheck **Pushes**
7. Click **Add webhook**

Well done, you have now set the webhook trigger from GitHub and the receiving endpoint on Azure DevOps. The next step is to create the pipeline which will receive the payload and create the WorkItem

### 3. Create a new Pipeline on Azure DevOps

1. In your Azure DevOps project got to **Pipelines > New pipeline**
2. Select where is your repo
3. Select the respective repo
4. Select **Starter pipeline**
5. Copy and place the content of [this file](https://github.com/colinsalmcorner/notification-dispatch-poc/blob/main/pipelines/webhook.yml) replacing the auto-generated content
6. Click **Save**

Voilà
