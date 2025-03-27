pipeline {
    agent {
        node {
            label 'maven'
        }
    }

environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
    stages {
        stage('build') {
            steps {
                echo "---------------- Build started -------------"
                sh 'mvn clean deploy'
                echo "---------------- Build Commpleted -------------"
            }
        }

        stage('test') {
            steps {
                echo "---------------- Unit Test started -------------"
                sh 'mvn surefix-report:report'
                echo "---------------- Unit Test Completed -------------"
            }
        }

        stage('SonarQube analysis') {
        environment {
            scannerHome = tool 'valaxy-sonarqube-scanner'
        }
            steps{
            withSonarQubeEnv('valaxy-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
            sh "${scannerHome}/bin/sonar-scanner"
    }
    }
    }
    }
}
