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
### Seting up the configs to run the containers locally via docker
- Setup the following environmental variables. These environmental variables will be read into docker-compose.yaml when *docker-compose up* is executed
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
  - No console errors on startup
  - Website loads on localhost:8100/
  - Images viewable on localhost:8100/
  - Users can be registered
  - New posts can be created

### Setup kubernetes cluster using KubeOne
1. Setup KubeOne

    Basically follow the [instructions](https://github.com/kubermatic/kubeone/blob/master/docs/quickstart-aws.md)
  setting up the infrastructure
    - create and edit terraform/tfvars, noting the 'cluster_name'
    - run
      ```
        terraform plan
        terraform apply
      ```
2. Installing to AWS
  - Create a 'config.yaml' based on the output of running ```'kubeone config print --full'```
  - Create a 'tf.json' via terraform ```'terraform output -json > tf.json'```
  - run ```'kubeone install config.yaml -t tf.json .'```
  - this process takes ~5 min
  - export ```'KUBECONFIG=$PWD/<cluster_name>-kubeconfig'``` as an env variable
  - scale up with ```'scale -n kube-system machinedeployment/<cluster_name>-pool1 --replicas=3'```
  - confirm with ```'kubectl get pods --all-namespaces '```

3. Applying settings to kubernetes
  Go to /udacity-c3-deployment/k8s
  - run `'kubectl apply -f <file_name.yaml>'` for the Kubernetes setting files:
    - aws-secret.yaml
    - env-secret.yaml
    - env-configmap.yaml
  - run `'kubectl apply -f <file_name.yaml>'` for the Kubernetes pods creation:
    - backend-feed-deployment.yaml
    - backend-user-deployment.yaml
    - reverse-deployment.yaml
  - run `'kubectl apply -f <file_name.yaml>'` for the Kubernetes services creation:
    - backend-user-service.yaml
    - backend-feed-service.yaml
    - reverseproxy-service.yaml
    - frontend-service.yaml 
  - wait ~2 min and run `'kubectl get pods'` to check if everything is running. If you see something stuck on pending, you can use `'kubectl describe <podname>'` for more clues
  - Verify the setup
    - run `'kubectl port-forward service/reverseproxy 8080:8080'`
    - run `'kubectl port-forward service/reverseproxy 8080:8080'`
    - Confirm the app works as expected at http://localhost:8100. Functionality includes logging in, registering, viewing the feed /w images, and upload images if logged in.
  
  







