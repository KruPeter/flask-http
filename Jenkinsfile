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
    withAWS(region: 'us-east-1') {
    sh """
        aws eks --region us-east-1 update-kubeconfig --name opsSchool-eks-project
        
        cat <<EOF | kubectl apply -f -
        apiVersion: v1
        kind: Service
        metadata:
          name: webapp-service
        spec:
          type: LoadBalancer
          selector:
            app: webapp 
          ports:
            - protocol: TCP
              port: 80
              targetPort: 5000
       ---
       apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: webapp-deployment
          labels:
            app: webapp
        spec:
          replicas: 3
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
                image: peterkr/opsschool-project
                ports:
                - containerPort: 5000
              restartPolicy: Always
        EOF
        """
      }
    }
    
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
