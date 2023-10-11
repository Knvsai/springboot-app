pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    
    environment {
        registry = "434594413818.dkr.ecr.us-east-1.amazonaws.com/my-repo"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Knvsai/springboot-app']])
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
        
        stage('Push into ECR'){
            steps{
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 434594413818.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 434594413818.dkr.ecr.us-east-1.amazonaws.com/my-repo:latest'
            }
        }
        
        stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                }
            }
        }
       }
    }
}
