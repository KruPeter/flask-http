node("linux"){
  stage("Get Repo from brach") {
    checkout scm
  }
  
  stage("Create Docker Image") {
    //Dockerfile
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
}
  stage("Build") {
   script {
      try{
        sh """kubectl apply -f apply opsschool-project"""
        echo "try"
      }catch(error){
        echo "catch"                  
      }
    } 
  }

stage("Test if pods is deployed") {
    sh """kubectl get pods > /home/ubuntu/getPods.log"""
    echo "test"     
}
