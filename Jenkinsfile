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
        export KUBECONFIG=/home/ubuntu/opsSchool-eks-project
        //aws eks --region us-east-1 update-kubeconfig --name opsSchool-eks-project
        kubectl apply -f deployment.yml
    '''
    }

  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
