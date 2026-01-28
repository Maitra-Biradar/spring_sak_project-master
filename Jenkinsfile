pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Maitra-Biradar/spring_sak_project-master.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                scp target/app.jar ubuntu@172.31.18.194:/opt/spring-app/app.jar

                ssh ubuntu@172.31.18.194 "
                    pkill -f '/opt/spring-app/app.jar' || true
                    nohup java -jar /opt/spring-app/app.jar > /opt/spring-app/app.log 2>&1 &
                "
                '''
            }
        }
    }
}
