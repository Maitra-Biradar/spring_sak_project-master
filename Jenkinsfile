pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        SPRING_SERVER = "ubuntu@172.31.16.113"
        DEPLOY_DIR = "/opt/springboot"
        JAR_NAME = "spring_sak_project-0.0.1-SNAPSHOT.jar"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Maitra-Biradar/spring_sak_project-master.git'
            }
        }

        stage('Build Project') {
            steps {
                sh '''
                    mvn clean package -DskipTests
                '''
            }
        }

        stage('Stop Existing App') {
            steps {
                sshagent(['spring-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $SPRING_SERVER "
                        pkill -f $JAR_NAME || true
                        "
                    '''
                }
            }
        }

        stage('Deploy Jar') {
            steps {
                sshagent(['spring-ssh-key']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no \
                        target/*.jar \
                        $SPRING_SERVER:$DEPLOY_DIR/$JAR_NAME
                    '''
                }
            }
        }

        stage('Start Application') {
            steps {
                sshagent(['spring-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $SPRING_SERVER "
                        cd $DEPLOY_DIR &&
                        nohup java -jar $JAR_NAME > app.log 2>&1 &
                        "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Spring Boot Application Deployed Successfully!"
        }
        failure {
            echo "❌ Deployment Failed. Check Jenkins Logs."
        }
    }
}
