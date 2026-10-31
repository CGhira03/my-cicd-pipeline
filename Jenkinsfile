pipeline {
    agent any

    tools {
        nodejs 'node7.8.0'
    }

    environment {
        IMAGE_TAG = 'v1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'node --version'
                sh 'npm install'
                sh 'CI=false npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'CI=true npm test'
            }
        }

        stage('Build docker image') {
            steps {
                script {
                    env.APP_ENV  = (env.BRANCH_NAME == 'main') ? 'main' : 'dev'
                    env.APP_PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    env.IMAGE    = "node${env.APP_ENV}:${env.IMAGE_TAG}"
                }
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f app-$APP_ENV || true'
                sh 'docker run -d --name app-$APP_ENV -p $APP_PORT:3000 $IMAGE'
                echo "App desplegada en http://localhost:${env.APP_PORT}"
            }
        }
    }
}