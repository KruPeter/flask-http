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
  
  stage("Prepare K8S yaml file") {
    sh """
      tee /home/ubuntu/pod.yml <<-'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: flask
  labels:
    app: flask
spec:
  containers:
  - name: flask
    image:  peterkr/opsschool-project:${env.BUILD_ID}
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
EOF
            """
      echo "build"
  }
  
  
  stage("Deploy") {                
    script {
      try{
        //kubernetesDeploy configs: 'ttt.yml', kubeConfig: [path: ''], kubeconfigId: 'k8s-test', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
        sh """kubectl apply -f /home/ubuntu/pod.yml > /home/ubuntu/startPod.log"""
        echo "try"
      }catch(error){
        echo "catch"                  
      }
    }        
  }
 
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
