pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    
    PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
}
    stages {
	stage ('Git Checkout') {
  steps {
      git 'https://github.com/adityanaidu20690/addy_devops_project.git'
     }
  }
        stage('maven') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube') {
            environment {
    
    scannerHome = tool 'sonartest'
}
            steps {
                withSonarQubeEnv('sonartest') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
            }
        }
    }
}
