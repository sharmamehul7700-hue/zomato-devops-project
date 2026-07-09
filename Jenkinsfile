pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        IMAGE_NAME = "zomato-app:v1"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-pat',
                    url: 'https://github.com/sharmamehul7700-hue/zomato-devops-project.git'
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    java -version
                    javac -version
                    mvn -version
                    docker --version
                    kubectl version --client
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f kubernetes/

                    kubectl rollout restart deployment/zomato-deployment

                    kubectl rollout status deployment/zomato-deployment --timeout=120s
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    kubectl get pods -o wide
                    kubectl get svc
                '''
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Zomato CI/CD Pipeline Completed Successfully!'
            cleanWs()
        }

        failure {
            echo 'Zomato CI/CD Pipeline Failed!'
            cleanWs()
        }
    }
}
