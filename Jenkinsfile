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
                    def pom  = readMavenPom file: 'pom.xml'
                    def repo = pom.version.endsWith('SNAPSHOT') ? 'test' : 'maven-releases'

                    nexusArtifactUploader(
                        nexusVersion:  'nexus3',
                        protocol:      'http',
                        nexusUrl:      '35.176.54.105:8081',
                        repository:    repo,
                        credentialsId: 'nexus',
                        groupId:       pom.groupId,
                        version:       pom.version,
                        artifacts: [
                            [
                                artifactId: pom.artifactId,
                                file:       "target/${pom.artifactId}-${pom.version}.${pom.packaging ?: 'jar'}",
                                type:       pom.packaging ?: 'jar'
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
