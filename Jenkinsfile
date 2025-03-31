def registry = 'https://kaungmyatcial.jfrog.io/'
def imageName = 'kaungmyatcial.jfrog.io/kmc-valaxy-docker-docker-local/ttrend-new'
def version   = '2.1.2'
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

        // stage('test') {
        //     steps {
        //         echo "---------------- Unit Test started -------------"
        //         sh 'mvn surefie-report:report'
        //         echo "---------------- Unit Test Completed -------------"
        //     }
        // }

    //     stage('SonarQube analysis') {
    //     environment {
    //         scannerHome = tool 'valaxy-sonarqube-scanner'
    //     }
    //         steps{
    //         withSonarQubeEnv('valaxy-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
    //         sh "${scannerHome}/bin/sonar-scanner"
    // }
    // }
    // }
        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifactory-creds"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "kmc-valaxy-libs-release-local/{1}",
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
                docker.withRegistry(registry, 'artifactory-creds'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }
    }
}
