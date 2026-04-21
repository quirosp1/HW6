pipeline {
    agent any
    tools {
        maven '3.9.11'
    }
    environment {
        NEXUS_URL = "host.docker.internal:8081" 
        NEXUS_REPOSITORY = "maven-snapshots" // Matches your 1.0-SNAPSHOT version
        NEXUS_CREDENTIAL_ID = "nexus-credentials-id"
    }
    stages {
        stage('Clone') {
            steps { checkout scm }
        }
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Push to Nexus') {
            steps {
                script {
                    // This matches your console log exactly
                    nexusArtifactUploader(
                        nexusVersion: "nexus3",
                        protocol: "http",
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "com.msu.peter",
                        artifactId: "hw5-app",
                        version: "1.0-SNAPSHOT",
                        repository: "${NEXUS_REPOSITORY}",
                        credentialsId: "${NEXUS_CREDENTIAL_ID}",
                        artifacts: [
                            [artifactId: "hw5-app",
                             classifier: '',
                             file: "target/hw5-artifact.jar",
                             type: 'jar']
                        ]
                    )
                }
            }
        }
    }
}
