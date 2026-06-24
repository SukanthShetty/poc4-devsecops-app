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
                withSonarQubeEnv('sonar') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=poc4-app \
                    -Dsonar.projectName=poc4-app
                    '''
                }
            }
        }
 
        stage('Upload to Nexus') {
            steps {
                sh 'mvn deploy'
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
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push skanthshetty/poc4-devsecops-app:latest
                    '''
                }
            }
        }
 
        stage('Deploy to K3s') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
 
    post {
        success {
            mail to: 'sukanthrshettysuku@gmail.com',
                 subject: "SUCCESS: ${env.JOB_NAME}",
                 body: "Build Successful: ${env.BUILD_URL}"
        }
 
        failure {
            mail to: 'sukanthrshettysuku@gmail.com',
                 subject: "FAILED: ${env.JOB_NAME}",
                 body: "Build Failed: ${env.BUILD_URL}"
        }
    }
}
 
