def registry = 'https://marvel07.jfrog.io/'
def imageName = 'marvel07.jfrog.io/marvel-docker-local/marvel'
def version = "2.1.2"
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
        stage("Build") {
            steps {
               echo "----------------------Build Started-------------------------" 
               sh 'mvn clean deploy -Dmaven.test.skip=true'
               echo "----------------------Build Completed-----------------------"
            }
        }
        stage("Test") {
            steps {
                echo "---------------------Unit Test Started---------------------"
                sh 'mvn surefire-report:report'
                echo "---------------------Unit Test Completed--------------------"
            }
        }
/*    stage("SonarQube analysis") {
    environment {
      scannerHome = tool 'marvel-sonar-scanner'
    }
    steps{
    withSonarQubeEnv('marvel-sonarqube-server') {
        sh "${scannerHome}/bin/sonar-scanner"
        }
    }
    }
    stage("Quality Gate"){
        steps {
            script {
    timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
       def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
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
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"JFrog-cred"
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
*/
    stage("Docker Build") {
        steps {
           script {
            echo '<--------------------Docker Build started------------------->'
            app = docker.build(imageName+":"+version)
            echo '<--------------------Docker Build Ends----------------->'
           }
        }
    }

       stage("Docker Publish") {
          steps {
            script {
              echo '<-------------------Docker Publish Started----------------->'
               docker.withRegistry(registry, 'JFrog-cred'){
                app.push()
              }
              echo '------------------Docker Publish Completed---------------->'
           } 
        }
    }

    stage("Approval for the Deployment on Kubernetes ") {
        steps {
            input "Please approve the Deployment on Kubernetes"
        }
    }

    stage("Deploy on Kubernetes") {
        steps{
            script{
                echo '<------------Deployment on Kubernetes Started----------->'
                sh './deploy.sh'
                echo '-------------Deployment on Kubernetes Ended------------->'
            }
        }
    }
  }
}