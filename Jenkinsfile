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
  
  stage('Apply Kubernetes files') {
    withAWS(region: 'us-east-1', profile: 'AWSK8S') {
sh """
aws eks update-kubeconfig --name opsSchool-eks-project
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
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
spec:
  selector:
    matchLabels:
      app: flask
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: peterkr/opsschool-project:${env.BUILD_ID}
        ports:
        - containerPort: 5000
EOF
"""
    }
  }
  
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
