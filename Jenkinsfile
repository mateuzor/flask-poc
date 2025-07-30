pipeline {
    agent none

    environment {
        IMAGE_NAME = "flask-app"
        IMAGE_TAG = "flask-app:${env.BUILD_NUMBER}"
        CHARTS_REPO = "https://github.com/mateuzor/helm-chart.git"
        CHARTS_DIR = "helm-chart"
        DEPLOY_NAMESPACE = "apps"
    }

    stages {
        stage('Install & Test') {
            agent {
                docker { image 'python:3.11' }
            }
            steps {
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
                sh 'pytest --junitxml=test-results.xml'
            }
            post {
                always {
                    junit 'test-results.xml'
                }
            }
        }
        stage('Build Docker Image') {
            agent any
            steps {
                sh 'eval $(minikube docker-env) && docker build -t flask-app:latest .'
            }
        }
        stage('Clone Helm Charts') {
            agent any
            steps {
                sh 'rm -rf $CHARTS_DIR'
                sh 'git clone $CHARTS_REPO'
            }
        }
        stage('Deploy') {
            agent any
            steps {
                sh 'helm upgrade --install flask-app $CHARTS_DIR/Flask-app --set image.repository=flask-app --set image.tag=latest --namespace $DEPLOY_NAMESPACE'
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}