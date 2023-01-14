pipeline{
    agent any
    
    stages {
        stage("Checkout") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/fayizv/helm-chart.git']]])
            }
        }  
        
       stage("Build Docker Image") {
            steps {
                script {
                    sh 'docker build -t fayizv/helm-sample .'
                    
                }
            }
        }
          
        
        stage('AWS ECR login & push') {
            steps {
                script {
                   
                        sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 707032823801.dkr.ecr.us-east-1.amazonaws.com'
                        sh 'docker tag fayizv/helm-sample:latest 707032823801.dkr.ecr.us-east-1.amazonaws.com/python-flask:${BUILD_NUMBER}' 
                        sh 'docker push 707032823801.dkr.ecr.us-east-1.amazonaws.com/python-flask:${BUILD_NUMBER}'
                }
            }
        }
        
        stage('Helm Push to ECR') {
            steps {
                sh 'echo version : 0.${BUILD_NUMBER}.0 >> chart/Chart.yaml'
                sh 'helm package chart'
                sh 'aws ecr get-login-password  --region us-east-1 | helm registry login --username AWS  --password-stdin 707032823801.dkr.ecr.us-east-1.amazonaws.com'
                sh 'helm push helm-sample-0.${BUILD_NUMBER}.0.tgz oci://707032823801.dkr.ecr.us-east-1.amazonaws.com/'
                sh 'rm -rf helm-sample-*'
                }
        }
       
    }
}
