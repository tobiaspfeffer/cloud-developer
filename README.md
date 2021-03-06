# Udagram Image Filtering Microservice

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into three parts:
1. [The Simple Frontend](/udacity-c3-frontend) a basic Ionic client web application which consumes the RestAPI Backend. 
2. [The RestAPI Feed Backend](/udacity-c3-restapi-feed), a Node-Express feed microservice.
3. [The RestAPI User Backend](/udacity-c3-restapi-user), a Node-Express user microservice.
3. [The Microservice Deployment](/udacity-c3-deployment), a travis CI based CI/CD pipeline which build and deployes the containerized application on a AWS kubernetes cluster.

## Deployment Setup

1. The containerized application is deployed to AWS EKS 
2. The following environment variables have to be set in travis CI's environment variables
    2.1. TPF_POSTGRESS_USERNAME - base64 encoded postgress_username of the AWS RDS
    2.2. TPF_POSTGRESS_PASSWORD - base64 encoded postgress_password of the AWS RDS
    2.3. TPF_AWS_CREDENTIALS - base64 encoded aws credentials for the S3 bucket uses in the backend
    2.4.  TPF_AWS_ACCESS_KEY_ID - plain text credentials for IAM role created AWS EKS
    2.5.  TPF_AWS_SECRET_ACCESS_KEY - plain text credentials for IAM role created AWS EKS
    2.6.  KUBE_CA_CERT - certificate for accessing EKS
    2.7.  DOCKER_USERNAME - username to docker hub
    2.8.  DOCKER_PASSWORD - password to docker hub
3. Release version of the containerized microservices can be set by increasing major.minor.patch number in version file located in the root of the directory
4. The build job runs on a sucessfull merge on master_dev
5. The travis CI pipeline is configured to collectively release new images of the microservices to docker hub taging each newly released image with the version number and additionally with latest tag
6. The containerized application running on the images with the latest tag is then deployed to EKS by first configuring kubectl to access the cluster
7. secrets and env variables are injected to the kubernetes cluster
8. The frontend is exposed via load balancer to public access
9. The backend uses a nginx proxy which is exposed via load balancer to the frontend and to the public
10. New versions are deployed using a rolling update in the travis CI pipeline
11. A/B deployment of the microservice xxx is possible by locally changing the name and image version in the file udacity-c3-deployment/k8s/xxx-deployment.yaml but leaving the selector name constant. A example for the frontend is provided in udacity-c3-deployment/k8s/frontendB-deployment.yaml



## Getting local Setup

> _tip_: this frontend is designed to work with the RestAPI backends). It is recommended you stand up the backend first, test using Postman, and then the frontend should integrate.

### Installing Node and NPM
This project depends on Nodejs and Node Package Manager (NPM). Before continuing, you must download and install Node (NPM is included) from [https://nodejs.com/en/download](https://nodejs.org/en/download/).

### Installing Ionic Cli
The Ionic Command Line Interface is required to serve and build the frontend. Instructions for installing the CLI can be found in the [Ionic Framework Docs](https://ionicframework.com/docs/installation/cli).

### Installing project dependencies

This project uses NPM to manage software dependencies. NPM Relies on the package.json file located in the root of this repository. After cloning, open your terminal and run:
```bash
npm install
```
>_tip_: **npm i** is shorthand for **npm install**

### Setup Backend Node Environment
You'll need to create a new node server. Open a new terminal within the project directory and run:
1. Initialize a new project: `npm init`
2. Install express: `npm i express --save`
3. Install typescript dependencies: `npm i ts-node-dev tslint typescript  @types/bluebird @types/express @types/node --save-dev`
4. Look at the `package.json` file from the RestAPI repo and copy the `scripts` block into the auto-generated `package.json` in this project. This will allow you to use shorthand commands like `npm run dev`


### Configure The Backend Endpoint
Ionic uses enviornment files located in `./src/enviornments/enviornment.*.ts` to load configuration variables at runtime. By default `environment.ts` is used for development and `enviornment.prod.ts` is used for produciton. The `apiHost` variable should be set to your server url either locally or in the cloud.

***
### Running the Development Server
Ionic CLI provides an easy to use development server to run and autoreload the frontend. This allows you to make quick changes and see them in real time in your browser. To run the development server, open terminal and run:

```bash
ionic serve
```

### Building the Static Frontend Files
Ionic CLI can build the frontend into static HTML/CSS/JavaScript files. These files can be uploaded to a host to be consumed by users on the web. Build artifacts are located in `./www`. To build from source, open terminal and run:
```bash
ionic build
```
***