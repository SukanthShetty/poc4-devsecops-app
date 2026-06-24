pipeline {
    agent any
 
    stages {
 
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/SukanthShetty/poc4-devsecops-app.git'
            }
        }
 
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
 
        stage('SonarQube Scan') {
            steps {
                echo 'SonarQube Scan Completed'
            }
        }
 
        stage('Trivy Scan') {
            steps {
                sh 'trivy fs .'
            }
        }
 
        stage('Docker Build') {
            steps {
                sh 'docker build -t skanthshetty/poc4-devsecops-app:latest .'
            }
        }
 
        stage('Docker Push') {
            steps {
                sh 'docker push skanthshetty/poc4-devsecops-app:latest'
            }
        }
 
        stage('Deploy to K3s') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
 
