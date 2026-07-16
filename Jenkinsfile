pipeline {

    agent any


    environment {

        AWS_REGION = "ap-south-1"

        AWS_ACCOUNT_ID = "317018178943"

        ECR_REPOSITORY = "react-vite-app"

        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"

        IMAGE_TAG = "${BUILD_NUMBER}"

        CLUSTER_NAME = "react-jenkins-cluster"

    }


    stages {


        stage('Checkout Code') {

            steps {

                checkout scm

            }

        }



        stage('Build Docker Image') {

            steps {

                sh """

                docker build \
                -t ${ECR_URI}:${IMAGE_TAG} .

                """

            }

        }



        stage('Login to Amazon ECR') {

            steps {

                sh """

                aws ecr get-login-password \
                --region ${AWS_REGION} | \
                docker login \
                --username AWS \
                --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com

                """

            }

        }



        stage('Push Docker Image to ECR') {

            steps {

                sh """

                docker push ${ECR_URI}:${IMAGE_TAG}

                """

            }

        }



        stage('Deploy to EKS') {

            steps {

                sh """

                aws eks update-kubeconfig \
                --region ${AWS_REGION} \
                --name ${CLUSTER_NAME}


                kubectl set image deployment/react-vite-deployment \
                react-vite-container=${ECR_URI}:${IMAGE_TAG}


                kubectl rollout status deployment/react-vite-deployment

                """

            }

        }


    }



    post {

        success {

            echo "Deployment Successful"

        }


        failure {

            echo "Deployment Failed"

        }

    }

}
