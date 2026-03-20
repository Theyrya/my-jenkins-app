pipeline {
    agent any
    environment {
        NETLIFY_AUTH_TOKEN = credentials('NETLIFY_AUTH_TOKEN')
        NETLIFY_SITE_ID = credentials('NETLIFY_SITE_ID')
    }
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
                sh 'netlify deploy --dir=build --prod --auth=$NETLIFY_AUTH_TOKEN --site=$NETLIFY_SITE_ID'
            }
        }
    }
}