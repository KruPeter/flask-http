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
kubernetesDeploy configs: 'deploy.yml', kubeConfig: [path: ''], kubeconfigId: 'AWSK8s', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
    sh '''
        kubectl apply -f deployment.yml
        kubectl set image deployment/flask flask=peterkr/opsschool-project:"${BUILD_NUMBER}" --record
    '''
    }

  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
