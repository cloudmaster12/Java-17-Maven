pipeline {
    agent { label 'test' }
    

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        NEXUS_VERSION = "NEXUS3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "18.171.241.68:8081"
        NEXUS_REPOSITORY = "test"
        NEXUS_CREDENTIAL_ID = "Nexus"
    }

    stages {
        stage('Prepare') {
            
            steps {
                checkout scm
            }
        }

        stage('Compile') {
            
            steps {

                sh 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 && mvn verify package'
            }
        }

        stage('Unit Test') {
            
            steps {
                
                       sh 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 && mvn test'
                   
               
            }
            
        }
        
     }
        
}
