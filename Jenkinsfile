pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        SPRING_SERVER = "ubuntu@172.31.16.113"
        DEPLOY_DIR = "/opt/springboot"
        JAR_NAME = "app.jar"
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
                    mv target/*.jar target/app.jar
                '''
            }
        }

        stage('Deploy Jar') {
            steps {
                sshagent(['spring-ssh-key']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no \
                        target/app.jar \
                        $SPRING_SERVER:$DEPLOY_DIR/
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
                        nohup java -jar app.jar > app.log 2>&1 &
                        "
                    '''
                }
            }
        }
    }
}
