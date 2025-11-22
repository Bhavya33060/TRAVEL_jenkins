pipeline {
    agent any

    tools {
        jdk "JAVA_HOME"
        maven "MAVEN_HOME"
    }

    environment {
        NODEJS_HOME = tool name: 'NODE_HOME', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'

        TOMCAT_URL = 'http://localhost:6060/manager/text'
        TOMCAT_USER = 'root'
        TOMCAT_PASS = 'adminadmin'

        FRONTEND_DIR = 'travel-frontend'
        BACKEND_DIR = 'travel-backend'

        FRONTEND_WAR = 'travel-frontend/dist.war'
        BACKEND_WAR = 'travel-backend/target/travel.war'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Bhavya33060/TRAVEL_jenkins.git', branch: 'main'
            }
        }

        stage('Build Backend (Maven)') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend (React)') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    withEnv(["PATH+NODE=${NODEJS_HOME}/bin"]) {
                        bat 'npm install --legacy-peer-deps'
                        bat 'npm run build'
                    }
                }
            }
        }

        stage('Package Frontend to WAR') {
            steps {
                script {
                    bat "if exist ui rmdir /S /Q ui"
                    bat "mkdir ui"
                    bat "xcopy /E /Y /I \"${env.FRONTEND_DIR}\\dist\\*\" ui"
                    bat "jar -cvf ${env.FRONTEND_WAR} -C ui ."
                }
            }
        }

        stage('Deploy WARs to Tomcat') {
            steps {
                bat "curl -u ${TOMCAT_USER}:${TOMCAT_PASS} --upload-file \"${BACKEND_WAR}\" \"${TOMCAT_URL}/deploy?path=/travel-be&update=true\""
                bat "curl -u ${TOMCAT_USER}:${TOMCAT_PASS} --upload-file \"${FRONTEND_WAR}\" \"${TOMCAT_URL}/deploy?path=/travel-ui&update=true\""
            }
        }
    }
}

