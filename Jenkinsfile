pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        APP_NAME   = "springboot-app.jar"
        DEPLOY_DIR = "/opt/springboot"
        SERVER_USER = "ubuntu"
        SERVER_IP   = "3.110.141.135"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Maitra-Biradar/spring_sak_project-master.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Spring Boot Server') {
            steps {
                sh """
                scp target/*.jar ${SERVER_USER}@${SERVER_IP}:${DEPLOY_DIR}/${APP_NAME}
                ssh ${SERVER_USER}@${SERVER_IP} '
                    pkill -f ${APP_NAME} || true
                    nohup java -jar ${DEPLOY_DIR}/${APP_NAME} > app.log 2>&1 &
                '
                """
            }
        }
