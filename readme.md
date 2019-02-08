# PetClinic Docker Build Pod Template Example for GKE On-Prem

This is an example of running an existing Jenkins 2 master on a VM with a GKE On-Prem cluster using the [Kubernetes plugin](https://github.com/jenkinsci/kubernetes-plugin). This allows running an agent pod template on the cluster to handle pipeline tasks. See the [podTemplate folder](https://github.com/jefferyfry/spring-petclinic-docker-build-podTemplate-gkeonprem2/tree/master/podTemplate) and the [Jenkinsfile](https://github.com/jefferyfry/spring-petclinic-docker-build-podTemplate-gkeonprem2/blob/master/Jenkinsfile) for details. This pipeline runs a maven build, docker build, pushes the image to docker hub and then deploys the application to the same cluster. The petclinic application is then externally accessible via a [service](https://github.com/jefferyfry/spring-petclinic-docker-build-podTemplate-gkeonprem2/blob/master/petclinic-service.yaml).

![gke-on prem - jenkins 2 on-prem](https://user-images.githubusercontent.com/6440106/52494815-d376d000-2b83-11e9-852a-c31a99367ce9.png)



