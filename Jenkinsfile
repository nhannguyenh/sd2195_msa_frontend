pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '211125338837'
        AWS_DEFAULT_REGION = 'ap-southeast-1'
        IMAGE_REPO_NAME = 'sd2195_ecr_frontend'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage ("Build image") {
            steps {
                sh ('docker build -t ${IMAGE_REPO_NAME} .')
            }
        }
        stage ("Login to ECR") {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'AWS_CREDS', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh ('aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com')
                }
            }
        }
        stage ("Push image") {
            steps {
                sh ('docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}')
                sh ('docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}')
            }
        }
        stage ("Clear image") {
            steps {
                sh ("docker rmi -f \$(docker images | grep ${IMAGE_REPO_NAME} | awk '{print \$3}' | uniq)")
            }
        }
    }
}