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
  
   stage("verify dockers") {
  sh "docker images"
 }
	
stage('Apply Kubernetes files') {
    withAWS(region: 'us-east-1') {
sh """
aws eks update-kubeconfig --name test-cluster

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  labels:
    app: webapp
spec:
  type: NodePort
  selector:
    app: webapp 
  ports:
    name: http
    port: 80
    targetPort: 5000
    nodePort: 32000
    protocol: TCP
	  
EOF
"""
    }
  }
}
