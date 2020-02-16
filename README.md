# Project 4: Refactor Udagram App into Microservices and Deploy

Containerizing the Instagram clone, setting it up with kubernetes on AWS, and linking the CI/CD to this github repo.

## Prerequisites:
1. Install Docker locally along with docker-cli
2. With an AWS IAM account, ensure the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY values can be found in the file ~/.aws/credentials, 
   which is needed by the docker image ```alwang85/udacity-restapi-feed``` when issue getSignedUrl request to AWS.
   Also, export the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY values in .bash_profile, which are needed by the terminal
   when execute Terraform commands to create the infrastructure and KubeOne to install Kubernetes cluster 
   and create worker nodes. (credits to [Mark B for the troubleshooting tips](https://docs.google.com/document/d/1r25X8ckOhZ_8k_f0hNg_bCGozG2lYvX4x2A8yT-UT0o/edit))
3. Follow the [kubeone guide](https://github.com/kubermatic/kubeone/blob/master/docs/quickstart-aws.md) to install the terraform cli and kubeone cli

## Setup Instructions
1. Seting up the configs to run the containers locally via docker
  - Setup the following environmental variables. These environmental variables will be read into docker-compose.yaml
    when *docker-compose up* is executed
    ```
    export AWS_BUCKET=
    export AWS_PROFILE=
    export AWS_REGION=
    export JWT_SECRET=
    export POSTGRESS_DATABASE=
    export POSTGRESS_HOST=
    export POSTGRESS_PASSWORD=
    export POSTGRESS_USERNAME=
    export URL=
    ```

  - Build the docker images:
  ```
    cd udacity-c3-deployment/docker
    docker-compose -f docker-compose-build.yaml build --parallel
  ```

  - Run the docker images:
  ```
    #assuming you are still in udacity-c3-deployment/docker
    docker-compose up
  ```

  - Confirm application runs as expected
  1. No console errors on startup
  2. Website loads on localhost:8100/
  3. Images viewable on localhost:8100/
  4. Users can be registered
  5. New posts can be created

