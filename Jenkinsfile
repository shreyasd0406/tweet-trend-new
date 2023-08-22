pipeline {
    agent {
        node {
           label 'maven'
        }
    }

    stages {
        stage('Clone-code') {
            steps {
               git branch: 'master', url: 'https://github.com/shreyasd0406/tweet-trend-new.git'
            }
        }
    }
}
