node("linux"){
  stage("Get Repo from brach") {
    checkout scm
  }
  
  stage("Create Docker Image") {
    app = docker.build("peterkr/opsschool-project")
  }

  stage("verify Docker Image") 
  {
    sh "docker images"
  }

  stage("Push to DockerHub") {
    withDockerRegistry(credentialsId: 'dockerhub.peter.krumer', url: '') 
    {
      app.push()
    }
  }
  stage('Slack it'){
    slackSend color: slackColor, message: "Build finished with ${end}: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
  }
}
