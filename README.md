<img src="https://github.com/didier-durand/gcp-workflows-on-github/blob/master/img/g_cloud.png" height="200">

# Google Cloud SDK workflows on Github CI/CD

Started from https://github.com/didier-durand/gcp-workflows-on-github

## GCP workflows:

The YAML files below are located in directory [/.github directory](https://github.com/didier-durand/gcp-workflows-on-github/tree/master/.github/workflows):


1. **[create-gke-cluster.yml](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/.github/workflows/create-gke-cluster.yml)**: workflow to test automated deployment of a K8s service on [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/docs/concepts/kubernetes-engine-overview). It automatically creates a cluster of 3 nodes, triggers a service deployment based on a public Docker image [tutum/hello-world](https://hub.docker.com/r/tutum/hello-world/)by applying this [YAML description](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/kubernetes/tutum-hello-world.yaml), check its proper functionning (both http & https) and finally deletes the cluster. The service configuration is based on a [Kubernetes external LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/) in order to obtain an IP address accessible via cURL from anywhere on the Internet.
3. **[push-to-gcr.yml](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/.github/workflows/push-to-gcr.yml)**: workflow to test the build of an image on Github and its push to [Google Container Registry](https://cloud.google.com/container-registry/docs/overview). Then, the image is decribed. Finally, it is deleted for cleanup of test environment.
4. **[gcloud-build.yml](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/.github/workflows/gcloud-build.yml)**: workflow to build a Docker image directly on [Google Build](https://cloud.google.com/cloud-build) (rather than via Github CI/CD if one ) and then push it to GCR. **Tip**: the final step describes the build just done: the id of the build to describe is captured by use of --format(value) in an additional list request.
5. **[gcloud-iam.yml](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/.github/workflows/gcloud-iam.yml)**: workflow to obtain [Identity & Access Management (IAM)](https://cloud.google.com/iam) information: list of service accounts on project, describe a specific service account, list keys attached 
8. **[gcloud-filestore.yml](https://github.com/didier-durand/gcp-workflows-on-github/blob/master/.github/workflows/gcloud-filestore.yml)**: workflow to interact with Filestore service of [Google Cloud Storage](https://cloud.google.com/storage): it lists existing buckets, creates a new bucket, writes files to it, read files from it, lists bucket content and deletes it.

Go to the [Actions tab](https://github.com/didier-durand/gcp-workflows-on-github/actions) to see the results of their last executions. Github does a very good job in protecting the defined secrets: each time the value of ${{ secrets.GCP_PROJECT }} is written in output streams, the Github writer will replace it with ***. Remember it as you read the job executions logs in Actions.

## setup for forks:

When you fork this repository to run it on your own, you will need to recreate two [Github secrets](https://docs.github.com/en/actions/configuring-and-managing-workflows/using-variables-and-secrets-in-a-workflow) in your own repository to get working workflows: 

- ${{ secrets.GCP_PROJECT }} for the GCP project in which you want to execute your tests  
- ${{ secrets.GCP_SA_KEY }} for the secret key of the GCP Service Account(SA) under the behalf of which you want to execute the various GCP commands.

Of course, you need to define the corresponding GCP project and service account as part of your project in your global GCP account so that workflows can properly interact with GCP.

The service account requires following privileges (given via IAM interface) to run the various workflows:

- Service Account User
- Security Reviewer
- Kubernetes Engine Admin
- Cloud Build Editor
- Cloud Build Service Account
- Cloud Run Admin
- Storage Admin
- Storage Object Creator
- Storage Object Viewer
- Logging Admin
- Compute Admin

Also, you need to activate in your GCP project the APIs corresponding to the privileges above in order to use the services. To make things more direct (but less secure...), you can also give the role of Project Owner to your service account in order to obtain all privileges on all resources and services.

Those privileges can probably be tightened - Admin privileges are overkill for some workflows - for productive use of those workflows: minimal security rights are not the priority here.
