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
                echo "---------build started-------------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "---------build completed-------------"
            }
        }
        stage('test') {
            steps {
                echo "---------unit test started-------------"
                sh 'mvn surefire-report:report'
                echo "---------unit test completed-------------"
            }
        }
        stage('SonarQube analysis') {
            environment {
    
    scannerHome = tool 'sonartest'
}
            steps {
                withSonarQubeEnv('sonartest') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
            }
        }
        stage("Quality Gate"){
          timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
          }
        }
    }
}
