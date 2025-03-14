pipeline {
    agent any
    stages {
        stage('Stage: Checkout') {
            steps {
                git credentialsId: 'gitlab_user_threepoints', url: 'https://github.com/juangaav/threepoints_devops_webserver'
            }
        }
        stage('Stage: Pruebas de SAST') {
            steps {
                echo 'Ejecución de  pruebas de SAST'
            }
        }  
        stage('Build') {
            steps {
                script {
                    def dockerPath = '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"'
                    if (!fileExists(dockerPath.replaceAll('"', ''))) {
                        error "Docker executable not found at ${dockerPath}"
                    }
                    
                    // Execute the Docker build command
                    bat "${dockerPath} build -t devops_ws ."
                }
            }
        }
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'Sonar-scanner'
                    withSonarQubeEnv('Sonar Local') { // If you have configured more than one global server connection, you can specify its name
                        bat "${scannerHome}/bin/sonar-scanner"
                    }
                }      
            }
        }
    }
}
