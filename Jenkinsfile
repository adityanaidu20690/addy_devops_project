def registry = 'https://sonartest.jfrog.io'
def imageName = 'sonartest.jfrog.io/addydock-docker-local/ttrend'
   def version   = '2.1.4'
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
            steps{
                    script{
                        timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
          }
                    }
            }
          
        }
        
         stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    } 
    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

            stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'jfrog'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }  
    }
}
