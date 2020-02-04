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
    sh """
      tee /home/ubuntu/pod.yml <<-'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: opsschool-project
  labels:
    app: opsschool-project
spec:
  containers:
  - name: opsschool-project
    image: peterkr/opsschool-project
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
EOF
            """
      echo "build"           
}
stage("Deploy to k8s") {                
  script {
      try{
        kubernetesDeploy configs: '', kubeConfig: [path: ''], kubeconfigId: 'k8s.agent.01', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
        sh """kubectl apply -f /home/ubuntu/pod.yml > /home/ubuntu/startPod.log"""
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
