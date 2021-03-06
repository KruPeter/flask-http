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
	
stage('Deploy to k8s') {
    withAWS(region: 'us-east-1') {
sh """
aws eks update-kubeconfig --name test-cluster

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: webapp-lb
  labels:
    app: webapp
spec:
  ports:
  - port: 80
    targetPort: 5000
  selector:
    app: webapp
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  labels:
    app: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: peterkr/opsschool-project:${BUILD_ID}
        ports:
        - name: http
          protocol: TCP
          containerPort: 5000
      restartPolicy: Always
EOF
"""
    }
  }
}
