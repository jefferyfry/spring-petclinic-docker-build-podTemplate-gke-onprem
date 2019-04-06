
pipeline {
  agent {
    kubernetes {
        cloud 'gkeonprem'
        label 'docker-build-pod-onprem'
        yamlFile 'podTemplate/spring-petclinic-docker-build-onprem.yaml'
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
    stage('Staging') {
      steps {
        container('gcloud-kubectl'){
          withCredentials([string(credentialsId: 'gkeonprem-access-token', variable: 'TOKEN')]) {
            sh '''
                 kubectl --token=${TOKEN} delete namespace spring-petclinic-docker-build || true
                 sleep 5
                 kubectl --token=${TOKEN} create namespace spring-petclinic-docker-build
                 kubectl --token=${TOKEN} create deployment spring-petclinic-docker-build --image=jefferyfry/spring-petclinic:latest --namespace spring-petclinic-docker-build
                 kubectl --token=${TOKEN} apply -f petclinic-service.yaml
                 echo "http://10.0.10.253:8080/"
                 echo "Spring PetClinic Launched!"
                 
            '''
          }
        }
      }
    }
  }
}