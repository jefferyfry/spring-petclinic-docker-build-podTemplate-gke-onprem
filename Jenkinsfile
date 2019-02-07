
pipeline {
  agent {
    kubernetes {
        cloud 'gkeonprem'
        label 'docker-build-pod'
        yamlFile 'podTemplate/spring-petclinic-docker-build.yaml'
    }
  }
  stages {
    stage('Maven Install') {
      steps {
        container('maven') {
          sh 'mvn clean install'
        }
      }
    }
    stage('Docker Build') {
      steps {
        container('docker'){
          sh 'docker build -t jefferyfry/spring-petclinic:latest .'
        }
      }
    }
    stage('Docker Push') {
      steps {
        container('docker'){
          withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
            sh 'docker push jefferyfry/spring-petclinic:latest'
          }
        }
      }
    }
    stage('Launch on Staging Cluster') {
      steps {
        container('gcloud-kubectl'){
          withCredentials([file(credentialsId: 'key-sa', variable: 'GC_KEY')]) {
            sh "gcloud auth activate-service-account --key-file=${GC_KEY}"
            sh '''
                 gcloud config set project partner-demo-dev
                 gcloud container clusters get-credentials csp-gke --zone us-east1-b
                 kubectl delete namespace spring-petclinic-docker-build || true
                 sleep 5
                 kubectl create namespace spring-petclinic-docker-build
                 kubectl run spring-petclinic-docker-build --image=jefferyfry/spring-petclinic:latest --port 8080 --namespace spring-petclinic-docker-build
                 kubectl expose deployment spring-petclinic-docker-build --type=LoadBalancer --port 8092 --target-port 8080 --namespace spring-petclinic-docker-build
                 while [ -z "$url" ]; do url=$(kubectl describe service spring-petclinic-docker-build --namespace spring-petclinic-docker-build | grep 'LoadBalancer Ingress:' | awk '{printf "http://%s:8092",$3;}'); sleep 2; done
                 echo "$url"
                 echo "Spring PetClinic Launched!"
                 
            '''
          }
        }
      }
    }
  }
}