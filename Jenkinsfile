pipeline {
    agent any
    
    // Add this block to fix the 'mvn: not found' error
    tools {
        maven '3.9.11' 
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        // Since you are on a Mac, use this to let the Jenkins container 
        // talk to the Nexus container/host
        NEXUS_URL = "host.docker.internal:8081" 
        NEXUS_REPOSITORY = "maven-releases"
        NEXUS_CREDENTIAL_ID = "nexus-credentials-id"
    }

    stages {
        stage('Clone Assignment') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                // Now that the tool is defined, this command will work
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    // The 'target' folder is created by the Build stage
                    def artifactPath = "target/${pom.artifactId}-${pom.version}.jar"

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
        success { echo 'Done!' }
        failure { echo 'Pipeline failed.' }
    }
}
