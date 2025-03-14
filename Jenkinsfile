pipeline {
    agent any
    stages {
        stage('Stage: Checkout') {
            steps {
                git credentialsId: 'gitlab_user_threepoints', url: 'https://github.com/juangaav/threepoints_devops_webserver'
            }
        }
        stage('Parallel Stages') {
            parallel {
                stage('Stage: Pruebas de SAST') {
                    steps {
                        echo 'Ejecución de  pruebas de SAST'
                    }
                }
                stage('Imprimir Env') {
                    steps {
                        script {
                            echo "El WORKSPACE es: ${env.WORKSPACE}"
                        }
                    }
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    def dockerPath = '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"'
                    if (!fileExists(dockerPath.replaceAll('"', ''))) {
                        error "Docker executable not found at ${dockerPath}"
                    }
                    
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
                    withSonarQubeEnv('Sonar Local') {
                        bat "${scannerHome}/bin/sonar-scanner"
                    }
                }      
            }
        }
    }
}
