pipeline {
    agent any
    stages {
        stage('Install') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }
        stage('Deploy') {
    steps {
        sh 'nohup serve -s build -l 3000 > /dev/null 2>&1 &'
        echo 'App deployed at http://localhost:3000'
    }
}
    }
}