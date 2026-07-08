pipeline {

    agent any

    tools {
        maven 'Maven'
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
                echo 'Building Zomato Spring Boot Application'
                sh 'mvn clean package'
            }
        }


        stage('Test') {
            steps {
                echo 'Running Unit Tests'
                sh 'mvn test'
            }
        }


        stage('Docker Build') {
            steps {
                echo 'Building Docker Image'
                sh 'docker build -t zomato-app:v1 .'
            }
        }


        stage('Docker Image Check') {
            steps {
                echo 'Checking Docker Image'
                sh 'docker images | grep zomato-app'
            }
        }


        stage('Archive') {
            steps {
                echo 'Archiving JAR File'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

    }


    post {

        success {
            echo 'Zomato CI Pipeline Completed Successfully 🚀'
        }

        failure {
            echo 'Zomato CI Pipeline Failed ❌'
        }

    }

}
