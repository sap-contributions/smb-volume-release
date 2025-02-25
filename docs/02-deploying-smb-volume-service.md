---
title: Deploying to CloudFoundry
expires_at: never
tags: [smb-volume-release]
---

<!-- vim-markdown-toc GFM -->

* [Deploying to CloudFoundry](#deploying-to-cloudfoundry)
    * [Pre-requisites](#pre-requisites)
    * [Redeploy Cloud Foundry with SMB enabled](#redeploy-cloud-foundry-with-smb-enabled)
* [Testing or Using this Release](#testing-or-using-this-release)
    * [Deploying the Test SMB Server (Optional)](#deploying-the-test-smb-server-optional)
    * [Register smbbroker](#register-smbbroker)
    * [Testing and General Usage with smbbroker](#testing-and-general-usage-with-smbbroker)
    * [Follow the cf docs to deploy and test a sample app](#follow-the-cf-docs-to-deploy-and-test-a-sample-app)

<!-- vim-markdown-toc -->
# Deploying to CloudFoundry

## Pre-requisites

1. Install Cloud Foundry, or start from an existing CF deployment.  If you are starting from scratch, the article 
    [Overview of Deploying Cloud Foundry](https://docs.cloudfoundry.org/deploying/index.html) provides detailed
    instructions.

## Redeploy Cloud Foundry with SMB enabled

1. You should have it already after deploying Cloud Foundry, but if not clone the cf-deployment repository from git:

    ```bash
    $ cd ~/workspace
    $ git clone https://github.com/cloudfoundry/cf-deployment.git
    $ cd ~/workspace/cf-deployment
    ```

2. Now redeploy your cf-deployment while including the smb ops file:
    
   ```bash
    $ bosh -e my-env -d cf deploy cf.yml -v deployment-vars.yml -o operations/enable-smb-volume-service.yml
    ```
    
> [!NOTE]
> The above command is an example, but your deployment command should match the one you used to deploy Cloud 
Foundry initially, with the addition of a `-o operations/enable-smb-volume-service.yml` option.

Your CF deployment will now have a running service broker and volume drivers, ready to mount or create SMB volumes.  
Unless you have explicitly defined a variable for your broker password, BOSH will generate one for you.

# Testing or Using this Release

## Deploying the Test SMB Server (Optional)

If you do not have an existing SMB Server then you can optionally deploy the test SMB server bundled in this release.

The easiest way to deploy the test server is to include the `enable-smb-test-server.yml` operations file when you deploy
Cloud Foundry, also specifying `smb-username` and `smb-password` variables:

   ```bash
   $ bosh -e my-env -d cf deploy cf.yml -v deployment-vars.yml \
     -v smb-username=smbuser \
     -v smb-password=something-secret \
     -o operations/enable-smb-volume-service.yml \
     -o operations/test/enable-smb-test-server.yml
   ```

## Register smbbroker

* Deploy and register the broker and grant access to its service with the following command:

    ```bash
    $ bosh -e my-env -d cf run-errand smbbrokerpush
    $ cf enable-service-access smb
    ```

## Testing and General Usage with smbbroker

You can refer to the [Cloud Foundry docs](https://docs.cloudfoundry.org/devguide/services/using-vol-services.html#smb) 
for testing and general usage information.

## Follow the cf docs to deploy and test a sample app

Test instructions are [here](https://docs.cloudfoundry.org/devguide/services/using-vol-services.html#smb-sample)
The smbbroker uses credhub as a backing store, and as a result, does not require separate scripts for backup and restore,
since credhub itself will get backed up by BBR.

