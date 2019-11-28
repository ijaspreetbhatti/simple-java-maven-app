pipeline {
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
                docker.withRegistry('https://hub.docker.com', 'dockerID') {
                    def customImage = docker.build("my-image:${env.BUILD_ID}")
                    /* Push the container to the custom Registry */
                    customImage.push()
                }
            }
        }
    }
}