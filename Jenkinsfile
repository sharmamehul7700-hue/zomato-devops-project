pipeline {

    agent any

    tools {
        maven 'Maven'
    }

    environment {

        AWS_REGION = "ap-south-1"

        ECR_REPOSITORY =
        "356627769525.dkr.ecr.ap-south-1.amazonaws.com/zomato-app"

        IMAGE_NAME = "zomato-app:v1"

        ECR_IMAGE =
        "356627769525.dkr.ecr.ap-south-1.amazonaws.com/zomato-app:v1"

        KUBECONFIG = "/etc/rancher/k3s/k3s.yaml"
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
                mvn -version
                docker --version
                kubectl version --client
                aws --version
                aws sts get-caller-identity
                kubectl get nodes
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

                sh '''

                docker build \
                -t ${IMAGE_NAME} .

                '''

            }
        }



        stage('ECR Login') {

            steps {

                sh '''

                aws ecr get-login-password \
                --region ${AWS_REGION} | \
                docker login \
                --username AWS \
                --password-stdin \
                356627769525.dkr.ecr.ap-south-1.amazonaws.com

                '''

            }
        }



        stage('Push Image to ECR') {

            steps {

                sh '''

                docker tag \
                ${IMAGE_NAME} \
                ${ECR_IMAGE}


                docker push \
                ${ECR_IMAGE}

                '''

            }
        }



        stage('Deploy to Kubernetes') {

            steps {

                sh '''

                kubectl apply -f kubernetes/


                kubectl rollout restart \
                deployment/zomato-deployment


                kubectl rollout status \
                deployment/zomato-deployment \
                --timeout=120s

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

    }



    post {

        success {

            echo "Zomato Deployment with AWS ECR Completed Successfully!"

        }


        failure {

            echo "Zomato Pipeline Failed!"

        }

    }

}
