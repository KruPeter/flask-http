node("linux"){
  stage("Get Repo from brach") {
    git branch: 'master',
    url: 'https://github.com/KruPeter/flask-http.git' 
    checkout scm
  }
  
 stage("build docker") {
    customImage = docker.build("peterkr/opsschool-project:${env.BUILD_ID}")
    withDockerRegistry(credentialsId: 'dockerhub.peter.krumer') {
        customImage.push()
    }
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
    //kubernetesDeploy(configs: 'deployment.yml', enableConfigSubstitution: true)
    withAWS(region: 'us-east-1', credentials: 'awsk8s-1'){
      sh """
      cat <<EOF | kubectl apply -f -
apiVersion: v1      # for versions before 1.9.0 use apps/v1beta2
kind: Service
metadata:
  name: flask-service
  labels:
    app: flask
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
  selector:
    app: flask
---
      """
    }
    }
  
  stage("expose service") { // Expose the app to the world
    kubernetesDeploy(configs: 'service.yml', enableConfigSubstitution: true)
    }
  
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
