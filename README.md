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

## What are the steps

First things first we need to copy all the file in this git repo to your envionment.

- in cloud shell run the following command to copy this git repository to your environment
    ```
    git clone github.com/dan-brodie/try-new-things-gcp
    ```

We want to deploy our application using cloud run. In order to do this we need the application to be packaged up as a (docker) container.  Ideally if we make any changes to the application we want a system to detect this and automatically package up a new version.

We can do this by combining source code management (git) and cloud build (CI).  
Cloud build can watch for a trigger or a new commit and automatically build a new version when this happens.

We're going to tell google to follow the instructions in the `dockerfile` to build the application and store the created image in the Google Container Registry every time this trigger occurs.

We could do this manually by running the following command in the terminal

```
docker build -t myapplication .
docker push myapplication ...
```

But we really want to automate this process, we don't want an engineer to have to log in pull the latest changes and manually build the software.

Were we talking about a larger product we might refer to this process as integration. You may have heard the term Continuous Integration or CI which simply means the automation of the process of building the sub-systems of an application in order than they can be deployed.

The CI Tooling we are going to use for this process is Google Cloud Build.

Cloudbuild can be easily defined as a set of instructions in a `cloudbuild.yaml` file.



