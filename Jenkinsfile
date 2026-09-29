pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }
    
    triggers {
	githubPush()
	
	}	

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
	 

	  stage ('SonarQube Analysis') {
		steps {
                     withSonarQubeEnv('SonarQube') {
                       sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.1.2184:sonar -Dsonar.projectKey=timesheet-devops -Dsonar.projectName=timesheet-devops -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml'
                } 
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
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
    }
}
