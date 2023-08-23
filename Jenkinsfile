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
    stage("SonarQube analysis") {
    environment {
      scannerHome = tool 'marvel-sonar-scanner'
    }
    steps{
    withSonarQubeEnv('marvel-sonarqube-server') {
        sh "${scannerHome}/bin/sonar-scanner"
        }
    }
    }
    }
}