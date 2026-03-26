pipeline {
    agent any
    environment{
        AWS_DEFAULT_REGION = 'us-east-2'
        AWS_DOCKER_REGISTRY = '393060838565.dkr.ecr.us-east-2.amazonaws.com'
        APP_NAME = 'final-image'
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:22-alpine'
                    // for the same docker image, reuse
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # list all files
                    ls -la
                    node --version
                    npm --version
                    # npm install
                    npm install
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:22-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }
        stage('Build My Docker Image'){
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""'
                }
            }
            steps{
                 withCredentials([usernamePassword(credentialsId: 'my-ecs-key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) 
                { 
                    sh '''
    yum install -y docker
    docker build -t $AWS_DOCKER_REGISTRY/$APP_NAME .
    aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_DOCKER_REGISTRY
    docker push $AWS_DOCKER_REGISTRY/$APP_NAME:latest
'''
                }
            }
        }
        stage('Deploy to AWS S3') {
    agent {
        docker {
            image 'amazon/aws-cli'
            reuseNode true
            args '-u root --entrypoint=""'
        }
    }
    steps {
        withCredentials([usernamePassword(credentialsId: 'my-ecs-key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            sh '''
                aws --version
                aws s3 sync build/ s3://dhairya-react-app --delete
            '''
        }
    }
}
    }
}