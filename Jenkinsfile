pipeline {
    agent any

    tools {
        jdk 'JDK21'
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

        stage('Build') {
            steps {
                echo 'Building Zomato Application...'
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo 'Running Unit Tests...'
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        stage('Import Image into K3s') {
            steps {
                echo 'Importing Docker image into K3s...'
                sh '''
                    docker save ${IMAGE_NAME} -o zomato-app.tar
                    k3s ctr images import zomato-app.tar
                    rm -f zomato-app.tar
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                sh '''
                    kubectl apply -f kubernetes/
                    kubectl rollout restart deployment/zomato-deployment
                    kubectl rollout status deployment/zomato-deployment
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking Kubernetes resources...'
                sh '''
                    kubectl get nodes
                    kubectl get pods
                    kubectl get svc
                '''
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '🎉 Zomato CI/CD Pipeline Completed Successfully!'
        }

        failure {
            echo '❌ Zomato CI/CD Pipeline Failed!'
        }

        always {
            cleanWs()
        }
    }
}
