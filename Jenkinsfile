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
    withDockerRegistry(credentialsId: 'dockerhub.peterkr', url: '') 
    {
      app.push()
    }
  }

  stage("Deploy") {                
    script {
      try{
        kubernetesDeploy configs: '', dockerCredentials: [[credentialsId: 'dockerhub.peterkr', url: 'https://hub.docker.com/repository/docker/peterkr/opsschool-project']], kubeConfig: [path: ''], kubeconfigId: 'k8s.agent.01', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
        sh "kubectl apply -f opsschool-project"
        echo "try"
      }catch(error){
        echo "catch"                  
      }
    }        
  }
  stage("Test if pods is deployed") {
        sh "kubectl get pods"
        echo "test"     
  }
}
