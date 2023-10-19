pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    stages {
        stage("Git Checkout") {
            steps {
                git branch: 'main', 
                changelog: false,
                 poll: false, 
                 url: 'https://github.com/Satyavenkat2813/pipeline_webhooks.git'
                
            }
        }
        stage("Unit Test") {
            steps {
                sh 'mvn test'
                
            }
        }
         stage("Integration testing") {
            steps {
                sh 'mvn verify -DskipUnitTest'
                
            }
        }
           stage("Maven Build") {
            steps {
                sh 'mvn clean install'
            }
        }
        stage("Static Code Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar') {
                    sh'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage("Quality Gate Status") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, 
                    credentialsId: 'sonar'
                }
            }
        }
        stage("Upload Artifact to nexus") {
            steps {
                script {
                    nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']],
                     credentialsId: 'nexus', 
                     groupId: 'com.example', 
                     nexusUrl: '172.31.15.220:8081', 
                     nexusVersion: 'nexus3', 
                     protocol: 'http', 
                     repository: 'Project1-Release', 
                     version: '1.0.0'
                }
            }
        }
    }
}