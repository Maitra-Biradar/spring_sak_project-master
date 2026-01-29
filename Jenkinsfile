pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'jdk17'
    }
    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"

        APP_NAME    = "springboot-app.jar"
        DEPLOY_DIR  = "/opt/springboot"
        SERVER_USER = "ubuntu"
        SERVER_IP   = "3.110.141.135"
    }

    stages {

        stage('Check Java') {
            steps {
                sh '''
                echo "JAVA_HOME=$JAVA_HOME"
                java -version
                mvn -version
                '''
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
                ssh ${SERVER_USER}@${SERVER_IP} "mkdir -p ${DEPLOY_DIR}"
                scp target/*.jar ${SERVER_USER}@${SERVER_IP}:${DEPLOY_DIR}/${APP_NAME}
                ssh ${SERVER_USER}@${SERVER_IP} "pkill -f ${APP_NAME} || true"
                ssh ${SERVER_USER}@${SERVER_IP} "nohup java -jar ${DEPLOY_DIR}/${APP_NAME} > ${DEPLOY_DIR}/app.log 2>&1 &"
                """
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful'
        }
        failure {
            echo '❌ Deployment Failed'
        }
    }
}
