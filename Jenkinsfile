pipeline {

    agent any

    tools {
        maven 'Maven3'
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
                sh 'mvn clean package'
            }
        }


        stage('Test') {
            steps {
                sh 'mvn test'
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
            echo 'Zomato Build Successful 🚀'
        }

        failure {
            echo 'Zomato Build Failed ❌'
        }

    }

}
