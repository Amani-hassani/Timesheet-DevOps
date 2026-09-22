pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -Dmaven.test.skip=true'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t amanihass/timesheet-devops:latest .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push amanihass/timesheet-devops:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f timesheet-app || true'
                sh 'docker run -d --name timesheet-app -p 8081:8080 amanihass/timesheet-devops:latest'
            }
        }
    }
}
