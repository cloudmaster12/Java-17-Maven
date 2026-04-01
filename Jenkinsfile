pipeline {
    agent any

    tools {
        maven 'Maven-3.8'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile -DskipTests -B'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test -B'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh 'mvn sonar:sonar -B'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Push to Nexus') {
            steps {
                script {

                    nexusArtifactUploader(
                        nexusVersion:  'nexus3',
                        protocol:      'http',
                        nexusUrl:      '35.176.54.105:8081',
                        repository:    'test',
                        credentialsId: 'nexus',
                        groupId:       'se.kth.compilers',
                        version:       '1.0-SNAPSHOT',
                        artifacts: [
                            [
                                artifactId: 'java-17-maven-nexus',
                                file:       "target/java-17-maven-nexus-1.0-SNAPSHOT.jar",
                                type:       'jar'
                            ]
                        ]
                    )
                }
            }
        }
    }

    post {
        success { echo 'CI Pipeline passed.' }
        failure { echo 'CI Pipeline failed.' }
        always  { cleanWs() }
    }
}
