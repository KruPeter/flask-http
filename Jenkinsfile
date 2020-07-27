node("linux"){
  stage("Get Repo from brach") {
    git branch: 'master',
    url: 'https://github.com/KruPeter/flask-http.git' 
    checkout scm
  }
  
  stage("docker build") {
    customImage = docker.build("peterkr/flask-http")
  }

  stage("verify Docker Image") 
  {
    sh "docker images"
  }

  stage("Push to DockerHub") {
    customImage = docker.build("peterkr/flask-http:${env.BUILD_ID}")
    withDockerRegistry(credentialsId: 'dockerhub.peter.krumer', url: '') 
    {
      customImage.push()
    }
  }
  

  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
