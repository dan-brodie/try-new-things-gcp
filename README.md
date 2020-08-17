# Presentation Codebase 17/08/2020

## Try New Things at CTS!

With the Launch of the companies shared values, we encourage everyone to try new things! Even if you are not technical just the act of having a go can help solidify your understanding of what the Professional Services teams do on a day to day basis.

This is a super simplified version of what our people get up to!

## Platform Engineer for an Hour!

The Platform Engineering team (DevOps) generally works alongside development teams to deploy the software they have written. We are specialists in building software delivery pipelines and delivering best practise platform architecture for large clients.

A customer project may include tens or even hundred of services that must be combined together to make a functioning system.

We're going to deploy one service here, and we're going to deploy it in the simplest way to a managed container service in GCP.

## Deploy your own wikipedia clone

Web applications are written in many languages, The most common being Java and JavaScript. While our Development teams will often use these, the Platform Engineering teams at CTS lean towards languages that favour the tasks we find ourselves completing on a day to day basis, most of our people prefer Python and Golang.

We're using some code from the golang example site - so if you are interested in learning a bit more about writing applications you can find a full tutorial for this code [here](https://golang.org/doc/articles/wiki/).

This is reflective of what our engineering teams might recieve from a client.

## What the files are

- main.go - this is the application itself, its very simple and exists in just one file!
- html files - these are templates that the application will use, if you know some HTML you could edit these to make the site look nice
- dockerfile - this is the build instructions for the container that the application will be packaged in. this gives us a standard format that many different systems will understand. For simplicity you can think of it like burning the file to a CD so you can take it with you. (theres more to it than that)
- readme.md - thats this file! its written in a format called markdown hence the .md.
- cloudbuild.yaml - instructions for cloud build!

And thats about it!

## Sign up for a free GCP account

1. Log into google with your personal account and get a free trial for GCP
2. log into the google console
3. create a new project [here](https://console.cloud.google.com/projectcreate)
4. Once you have your project (you can name it whatever you like) open the cloud console at this address https://console.cloud.google.com/ 
5. enable some api's - these are the google services we're going to use - you can do this by clicking [here](https://console.cloud.google.com/flows/enableapi?apiid=cloudbuild.googleapis.com,run.googleapis.com,containerregistry.googleapis.com,cloudresourcemanager.googleapis.com)
6. start cloud shell in the top right and you are ready to follow along

## What are we doing?

We want to deploy our application using cloud run. In order to do this we need the application to be packaged up as a (docker) container.  Ideally if we make any changes to the application we want a system to detect this and automatically package up a new version.

We can do this by combining source code management (git) and cloud build (CI).  
Cloud build can watch for a trigger or a new commit and automatically build a new version when this happens.

We're going to tell google to follow the instructions in the `dockerfile` to build the application and store the created image in the Google Container Registry every time this trigger occurs.

We could do this manually by running the following command in the terminal

```
docker build -t myapplication .
docker push myapplication ...
```
and then deploy this to cloudrun manually
```
gloud run deploy myapplication --image myapplication
```

But we really want to automate this process, we don't want an engineer to have to log in pull the latest changes and manually build the software.

Were we talking about a larger product we might refer to this process as integration. You may have heard the term Continuous Integration or CI which simply means the automation of the process of building the sub-systems of an application in order than they can be deployed.

The CI Tooling we are going to use for this process is Google Cloud Build.

Cloudbuild can be easily defined as a set of instructions in a `cloudbuild.yaml` file.

## What are the steps

First things first we need to copy all the files in this git repo to your envionment and then push it back to a google source repo, this lets you attach a trigger in cloudbuild.

- in cloud shell run the following command to copy this git repository to your environment
    ```
    git clone https://github.com/dan-brodie/try-new-things-gcp.git
    ```
- switch to the new directory thats been downloaded by typing
    ```
    cd try-new-things-gcp
    ```
- create a source repository in google - pick whatever name you like, you will need to substitute this in the next steps.
    ```
    gcloud source repos create [REPO_NAME]
    ```
- push the code to your new source repo - you need to add your repository as a new remoste location
    ```
    gcloud init && git config credential.helper gcloud.sh
    git remote add google \
    https://source.developers.google.com/p/[PROJECT_NAME]/r/[REPO_NAME]
    git push --all google

    ```
- create a trigger that looks at your copy of the code - let it know what to do by telling where the cloudconfig.yaml file is
    ```
    gcloud beta builds triggers create cloud-source-repositories \
    --repo=[REPO_NAME] \
    --branch-pattern=".*" \
    --build-config=cloudbuild.yaml \
    ```

- now google is secure so its not going to let the cloud builder service do whatver it wants in your project so you need to grant it some permissions. were going to set an access role so that cloudbuild can deploy to cloud run. to do this we need the project number as well as the project name you selected. we can get this by running
    ```
    gcloud projects list
    ```
    then substitute your details in below
    ```
    gcloud projects add-iam-policy-binding [PROJECT_NAME] \
    --member "serviceAccount:[PROJECT_NUMBER]@cloudbuild.gserviceaccount.com" \
    --role roles/run.admin

    gcloud iam service-accounts add-iam-policy-binding \
    [PROJECT_NUMBER]-compute@developer.gserviceaccount.com \
    --member="serviceAccount:[PROJECT_NUMBER]@cloudbuild.gserviceaccount.com" \
    --role="roles/iam.serviceAccountUser"
    ```
- test your trigger from the console [here](https://console.cloud.google.com/cloud-build/triggers) click run trigger then in the dashboard on the left you should be able to see the progress of the build!

## Marvel at your wonderful creation!

You should now be able to test your application - you should see that cloudbuilder has deployed your wiki app - switch to the cloud run console [here](https://console.cloud.google.com/run) click on your app and you should see an URL you can test

Append it with /view/anything e.g

https://wiki-o3kfw2crsq-ew.a.run.app/view/thisisathing

The application allows for /view and /edit if you view something that doesnt exist you can create it and it will exist from now on.

