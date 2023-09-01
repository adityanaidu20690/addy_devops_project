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
      git branch: 'main', url: 'https://<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>@github.com/adityanaidu20690/tweet-trend.git'
     }
  }
        stage('git clone') {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}
