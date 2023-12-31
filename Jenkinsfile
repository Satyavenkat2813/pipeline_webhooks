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
                    waitForQualityGate abortPipeline: true, 
                    credentialsId: 'sonar'
                }
            }
        }
        stage("Upload Artifact to nexus") {
            steps {
                script {
                    def readPomFile= readMavenPom file: 'pom.xml'
                    def nexusRepo= readPomFile.version.endsWith("SNAPSHOT") ? "Project1-Snapshots" : "Project1-Release"

                    nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']],
                     credentialsId: 'nexus', 
                     groupId: 'com.example', 
                     nexusUrl: '172.31.15.220:8081', 
                     nexusVersion: 'nexus3', 
                     protocol: 'http', 
                     repository: nexusRepo, 
                     version: "${readPomFile.version}"
                }
            }
        }
        stage("Docker image build") {
            steps {
                script {
                    sh 'sudo chmod 666 /var/run/docker.sock'
                    sh 'docker image build -t javapro1:v1.$BUILD_ID .'
                    sh 'docker image tag javapro1:v1.$BUILD_ID satyavenkat/javapro1:v1.$BUILD_ID'
                    sh 'docker image tag javapro1:v1.$BUILD_ID satyavenkat/javapro1:latest'
                }
            }
        }
        stage("Docker image Push") {
            steps {
                script {
                    withCredentials([string(credentialsId: 'docker', variable: 'docker_pass')]) {
                        sh 'docker login -u satyavenkat -p ${docker_pass}'
                        sh 'docker push satyavenkat/javapro1:v1.$BUILD_ID'
                        sh 'docker push satyavenkat/javapro1:latest'
                    }
                   
                }
            }
        }
        stage ("Owasp") {
            steps {
                script{
                    dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp'
                    dependencyCheckPublisher pattern: '**/depandance-check.xml'
                }
            }
        }

        stage("trivy"){
            steps {
                script{
                    sh'trivy image satyavenkat/javapro1:latest'
                }
            }
        }
    }
}