pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '346701285224'    
        ECR_REPO_NAME = 'akash'  // ✅ Removed invalid slash
        IMAGE_TAG = 'v2'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        FULL_IMAGE_NAME = "${ECR_REGISTRY}/${ECR_REPO_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[
                              url: 'https://github.com/shashi04/Jenkins-batch2.git'
                          ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $ECR_REPO_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage('Tag Image') {
            steps {
                script {
                    sh 'docker tag $ECR_REPO_NAME:$IMAGE_TAG $FULL_IMAGE_NAME'
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh 'docker push $FULL_IMAGE_NAME'
                }
            }
        }
    }
}
