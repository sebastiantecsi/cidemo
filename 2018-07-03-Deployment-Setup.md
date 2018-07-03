---
title: Deployment Setup
layout: post
author: set
permalink: /deployment-setup/
source-id: 1yb_zwuZzEZNbjUUkBWyqMGla2d0FSA3Lk-dSHXAV3CM
published: true
---
**Repository for code:** 

The repository is hosted in GutHub on this url [https://github.com/AdditionDev/ISSOPF](https://github.com/AdditionDev/ISSOPF)

And we are using simple branching strategy having the following branches defined: 

* Main branch for Development: **develop**

* Branch for QA setup: **qa**

* Branch for Production setup: **master**

**Build Definitions:**

For automatic build we are using Visual Studio Online.

The setup is here 

[https://additionglobal.visualstudio.com/ISS.OPF.v2/ISS.OPF.v2%20Team/_build](https://additionglobal.visualstudio.com/ISS.OPF.v2/ISS.OPF.v2%20Team/_build)

#1 The build for development is defined with this name **ISS.OPF.v2 to DEV Azure**

* Is connected to GitHub Repository ISSOPF on branch **develop** 

* And has a build trigger for when new changes are committed to branch develop

* A set of variables is defined so that build could be executed correctly

    * **BuildConfiguration:dev**

    * **BuildPlatform:any cpu**

    * **APPINSIGHTS_APIKEY:bdca8964-7f21-4417-a21d-7c897855722d**

    * **CI:true**

* The build steps are :

    * Use NuGet 4.4.1

    * NuGet Restore for ISS.OPF.sln

    * Build ISS.OPF.sln 

        * with arguments /p:DeployOnBuild=true /p:PublishProfile="dev" /p:ProfileTransformWebConfigEnabled=True

        * Publish profile dev in the project publishes the project files into a folder named **website**

    * Use Yarn 1.7.0

    * Yarn install for ISS.OPF.WebApp

    * Yarn build for ISS.OPF.WebApp

    * Yarn install for ISS.OPF.ApiSpec

    * gulp build for ISS.OPF.ApiSpec/gulpfile.js

    * Copy Files to artifacts/website from ISS.OPF.WebApp/build

    * Copy Files to artifacts/website from /website

    * Copy Files to artifacts/website from ISS.OPF.ApiSpec/dist

    * Publish Artifact: drop from $(build.artifactstagingdirectory)

    * Azure App Service Deploy: OPF-Dev from $(build.artifactstagingdirectory)/website

* The build has a Service Hook added to the build **ISS.OPF.v2 to DEV Azure**

    * We are using a Slack Service Hook in order to post build result message in channel #issopfv2_build. The messages will be in all cases: Succeeded, Faild and Stopped. 

