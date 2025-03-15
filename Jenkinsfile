pipeline {
    agent any
    parameters {
        credentials(name: 'Credentials_Threepoints', description: 'Credenciales de usuario y contraseña', credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl')
    }
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
                        script {
                            def scannerHome = tool 'Sonar-scanner'
                            withSonarQubeEnv('Sonar Local') {
                                bat "${scannerHome}/bin/sonar-scanner"
                            }
                            timeout(time: 30, unit: 'SECONDS') {
                                waitForQualityGate(abortPipeline: false)
                            }
                        }
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
        stage('Configurar archivo') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Credentials_Threepoints', passwordVariable: 'PASSWORD', usernameVariable: 'USER')]) {
                        def credentialsContent = """
                        [credentials]
                        user=${USER}
                        password=${PASSWORD}
                        """
                        writeFile file: 'credentials.ini', text: credentialsContent.trim()
                    }
                    archiveArtifacts artifacts: 'credentials.ini'
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
        stage('Despliegue del servidor') {
            steps {
                script {
                    def dockerPath = '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"'
                    
                    bat "${dockerPath} stop devops_ws || true"

                    bat "${dockerPath} run -d -p 8090:8090 --name devops devops_ws"
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
