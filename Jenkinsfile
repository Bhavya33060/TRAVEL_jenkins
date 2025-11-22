pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    environment {
        TOMCAT_URL   = 'http://localhost:6060/manager/text'
        TOMCAT_USER  = 'root'
        TOMCAT_PASS  = 'adminadmin'

        FRONTEND_DIR = 'travel-frontend'
        BACKEND_DIR  = 'travel-backend'

        BACKEND_WAR  = "${BACKEND_DIR}/target/travel.war"
        FRONTEND_WAR = "${FRONTEND_DIR}/dist.war"
    }

    stages {

        stage('Install Frontend Dependencies') {
            steps {
                script {
                    def nodeHome = tool name: 'NODE_HOME', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodeHome}/bin:${env.PATH}"
                }
                dir("${FRONTEND_DIR}") {
                    bat 'npm install --legacy-peer-deps'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir("${FRONTEND_DIR}") {
                    bat 'npm run build'
                }
            }
        }

        stage('Package Frontend WAR') {
            steps {
                script {
                    bat "cd ${FRONTEND_DIR} && jar -cvf dist.war -C dist ."
                }
            }
        }

        stage('Build Backend WAR') {
            steps {
                dir("${BACKEND_DIR}") {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Deploy Backend to Tomcat') {
            steps {
                bat "curl -u %TOMCAT_USER%:%TOMCAT_PASS% --upload-file \"%BACKEND_WAR%\" \"%TOMCAT_URL%/deploy?path=/travel-be&update=true\""
            }
        }

        stage('Deploy Frontend to Tomcat') {
            steps {
                bat "curl -u %TOMCAT_USER%:%TOMCAT_PASS% --upload-file \"%FRONTEND_WAR%\" \"%TOMCAT_URL%/deploy?path=/travel-ui&update=true\""
            }
        }
    }
}
