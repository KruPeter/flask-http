node("linux"){
  stage("Get Repo from brach") {
    checkout scm
  }
  
  stage("Create Docker Image") {
    customImage = docker.build("peterkr/opsschool-project")
  }

  stage("verify Docker Image") 
  {
    sh "docker images"
  }

  stage("Push to DockerHub") {
    customImage = docker.build("peterkr/opsschool-project:${env.BUILD_ID}")
    withDockerRegistry(credentialsId: 'dockerhub.peter.krumer', url: '') 
    {
      customImage.push()
    }
  }
  
      stage("deploy to EKS") {
    sh '''
        export KUBECONFIG=/home/ubuntu/kubeconfig_opsSchool-eks
        kubectl apply -f deployment.yml
        kubectl set image deployment/flask flask=peterkr/opsschool-project:"${BUILD_NUMBER}" --record
   	    //kubectl apply -f service.yml
        //kubectl apply -f loadbalancer.yml
        kubectl get svc phonebook-lb -o jsonpath="{.status.loadBalancer.ingress[*]['ip', 'hostname']}" > appUrl.txt
    '''
    }
 
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
