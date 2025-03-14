node {
  stage('SCM') {
    checkout scm
  }
  stage('SonarQube Analysis') {
    script {
    def scannerHome = tool 'Sonar-scanner'
        withSonarQubeEnv('Sonar Local') { // If you have configured more than one global server connection, you can specify its name
            bat "${scannerHome}/bin/sonar-scanner"
        }
    }      
  }
}
