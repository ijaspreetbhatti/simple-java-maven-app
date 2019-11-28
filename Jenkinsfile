import jenkins.model.*
jenkins = Jenkins.instance

pipeline {
    environment {
        registry = "ijaspreetbhatti/my-image"
    }
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                script{
                docker.withRegistry(registry, 'dockerhub') {
                    def customImage = docker.build( registry +":${env.BUILD_ID}")
                    customImage.push()
                }
                }
            }
        }
    }
}