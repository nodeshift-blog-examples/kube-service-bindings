# Node.js - MongoDB with mongodb client

## Prerequisites

- [OpenShift Cluster on SandBox](/README.md#setup-an-openshift-cluster-on-a-red-hat-sandbox)
<!-- or [OpenShift Cluster locally on your PC](/README.md#setup-an-openshift-cluster-locally-on-your-pc) -->

## Install Service Binding Operator

**\*_NOTE:_** In **OpenShift cluster Sandbox**, Service binding Operator is already installed so no further action is needed.

<!--
- [Instructions](../../README.md#install-service-binding-operator) -->

## Install OpenShift Database Access Operator

**\*_NOTE:_** In **OpenShift cluster Sandbox**, OpenShift Database Access Operator is already installed so no further action is needed.

<!-- **\*_NOTE:_** In **OpenShift locally** Database Access Operator is not yet available, so in that case use OpenShift Sandbox to continue with the tutorial. -->

## Create a MongoDB Database on MongoDB Atlas

1.  Create an account on **MongoDB Atlas**, if you don't already have one visit below link to register https://www.mongodb.com/cloud/atlas/register
1.  Sign in
1.  Create a new Project
    1.  Click on **Projects** _(in the left sidebar)_.
    1.  Click **New Project**.
    1.  Choose a **name** for your project and click **Next**.
    1.  Accept the defaults and click the **Create Project** button.
1.  Create a MongoDB Database
    1.  Click the **Build Database** button.
    1.  Choose the **FREE plan** and click **Create**
    1.  Accept the defaults and click **Create Cluster**.
1.  Create a User for the MongoDB Database
    1.  Click on **Database Access** _(left sidebar)_
    1.  Click **Add New Database User**
    1.  Enter a **username** and **password**.
    1.  In the **Database User Privileges** section add a **buit-in rule** of **read and write to any database**.
1.  Update Network Access
    1. Click on **Network Access** _(left sidebar)_
    1. Click **Add IP Address**
    1. Click **Allow Access From Anywhere** and then **Confirm**

## Allowing access between OpenShift and mongoDB Database with "OpenShift Database Access operator".

1.  Select the **Administrator** perspective _(Top of left sidebar)_
1.  Expand the **Data Services** navigation menu _(bottom of left sidebar)_, and click **Database Access**.
1.  Click on the **Configuration button** _(upper right)_ and Select **Import Database provider Account**
1.  Import Database Provider Account
    1.  Select **MongoDB Atlas Cloud Database Service** from the **Database provider** drop down menu
    1.  Enter the **Account Credentials***
    1.  Click **Import**.

\* _For additional help on filling out the Account Credentials, follow [these detailed instructions](https://access.redhat.com/documentation/en-us/red_hat_openshift_database_access/1/html-single/quick_start_guide/index#find-your-mongodb-atlas-account-credentials_rhoda-qsg) for each field of the form._

## Deploying the MongoDB Database as a Cloud-Hosted Database

1.  Visit the **Topology** view, by selecting the **Developer** perspective _(left sidebar menu)_ then **Topology**
1.  Click on **+Add** _(left sidebar menu)_
    1.  Choose **Cloud-Hosted Database**
    1.  Next, choose **MongoDB Atlas Cloud Database Service**
    1.  Then, click the **Add to Topology** button
    1.  Select your **Database instance**
    1.  Click the **Add to Topology** button on the bottom left
1.  Upon successful connection you are taken to the **Topology** view, where the Cloud-Hosted Database is deployed.

## Deploy Node.js App through the OpenShift UI

- [Instructions](../../README.md#deploy-nodejs-app-from-openshift-ui)

## Connect Node.js app with mongoDB - Binding

- [Instructions](../../README.md#connecting-nodejs-app-using-service-binding-operator)

## Interact with the Application

By visiting application's UI, you are able to interact with the application by adding, fetching, editing and removing fruits.

- [Further instructions](../../README.md#interact-with-the-application)

## kube-service-bindings usage for mongodb

For fetching populated data provided by Service Binding Operator under the /binding directory, we need to invoke `getBinding` function from `kube-service-bindings` npm package.

First we require/import `kube-service-bindings` package

```javascript
const serviceBindings = require("kube-service-bindings");
```

Then we invoke the geBinding function by specifying the type and the client.

```javascript
const { url, connectionOptions } = serviceBindings.getBinding(
  "MONGODB",
  "mongodb"
);
```

**_NOTE:_** _A full list of the supported types and clients, is available on the kube-service-bindings [documentation](https://github.com/nodeshift/kube-service-bindings#usage)_

We can now easily connect to our database by passing the destructured objects, provided by getBinding function.

```javascript
const mongoClient = new MongoClient(url, connectionOptions);
```

Due to kube-service-bindings throws an error in case binding data are not available, we should wrap getBinding invocation.

```javascript
const serviceBindings = require("kube-service-bindings");
const { MongoClient } = require("mongodb");

let url;
let connectionOptions;

try {
  ({ url, connectionOptions } = serviceBindings.getBinding(
    "MONGODB",
    "mongodb"
  ));
} catch (err) {
  url = "mongodb://root:password@127.0.0.1:27017";
  connectionOptions = { auth: { password: "password", username: "root" } };
}

const mongoClient = new MongoClient(url, connectionOptions);
```

Thats the exact code we use on the mongodb example [/lib/db/index.js](../mongodb/lib/db/index.js), for establishing a connection with the database.

## Viewing the logs

Follow the below instructions for further details on how to view the logs of the application

- [Instructions](../../README.md#viewing-logs-of-the-app)

## Application's File/Folder Structure

More information about the structure of the Node.js App, please follow below link:

- [Application's structure](../../README.md#nodejs-applications-folder-structure)
