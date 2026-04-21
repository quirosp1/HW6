pipeline {
    agent any

    environment {
        // Update these to match your local Nexus setup
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        // Use 'host.docker.internal' if Jenkins is in Docker, otherwise use your local IP
        NEXUS_URL = "host.docker.internal:8081" 
        NEXUS_REPOSITORY = "maven-releases"
        NEXUS_CREDENTIAL_ID = "nexus-credentials-id"
    }

    stages {
        stage('Clone Assignment') {
            steps {
                // Pulls the Spring Boot code from your HW6 GitHub repo
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                // Build the Spring Boot application
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    // Extract metadata from pom.xml for the upload
                    def pom = readMavenPom file: 'pom.xml'
                    def artifactPath = "target/${pom.artifactId}-${pom.version}.jar"

                    echo "Uploading ${artifactPath} to Nexus..."

                    nexusArtifactUploader(
                        nexusVersion: "${NEXUS_VERSION}",
                        protocol: "${NEXUS_PROTOCOL}",
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "${pom.groupId}",
                        version: "${pom.version}",
                        repository: "${NEXUS_REPOSITORY}",
                        credentialsId: "${NEXUS_CREDENTIAL_ID}",
                        artifacts: [
                            [artifactId: "${pom.artifactId}",
                             classifier: '',
                             file: "${artifactPath}",
                             type: 'jar']
                        ]
                    )
                }
            }
        }
    }
    
    post {
        success {
            echo 'Artifact successfully deployed to Nexus!'
        }
        failure {
            echo 'Pipeline failed. Check Jenkins logs and Nexus connectivity.'
        }
    }
}
