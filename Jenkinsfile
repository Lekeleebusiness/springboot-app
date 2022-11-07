
pipeline {
    agent any
    tools{
        maven "Maven3"
    }
    environment{
        registry = "633668106002.dkr.ecr.us-east-1.amazonaws.com/my-springboot-app"
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Lekeleebusiness/springboot-app']]])
            }
        }
        
        stage('Build Jar'){
            steps{
                sh "mvn clean install"
            }
        }
        
        stage('Build Image'){
            steps{
               script{
                   docker.build registry
               } 
            }
        }
        
        stage('Pushing to ECR'){
            steps{
                script{
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 633668106002.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 633668106002.dkr.ecr.us-east-1.amazonaws.com/my-springboot-app:latest"
                }
            }
        }
        
        stage('Deploy to K8S'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                    sh "kubectl apply -f eks-deploy-from-ecr.yaml"
                }
            }
        }
        
    }
}
