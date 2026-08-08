pipeline {
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean compile'
            }
        }
	stage('SonarQube Analysis') {
    	    steps {
                withSonarQubeEnv('SonarQube') {
                sh '''
                chmod +x mvnw
                ./mvnw sonar:sonar \
                -Dsonar.projectKey=enterprise-devsecops-platform \
                -Dsonar.projectName=enterprise-devsecops-platform
            '''
            }
       }
    }
        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }
	stage('Quality Gate') {
    	    steps {
                timeout(time: 5, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
    	}
    }
        stage('Package') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
