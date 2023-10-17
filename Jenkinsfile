pipeline{
	agent any
	tools {
		maven 'maven3'  
	}
	stages{
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
		stage("	Integration Testing") {
			steps {
				sh 'mvn verify -DskipUnitTests'
			}
		}
		stage("Maven Build") {
			steps {
				sh 'mvn clean install' 
			}
		}
		stage("sonarqube analysis") {
			steps {
				script{
					withSonarQubeEnv(installationName: 'sonarqube-scanner',credentialsId: 'sonar') {
						sh 'mvn clean package sonar:sonar'
					}
				}
			}
		}
		stage("Quality gate status"){
			steps{
				script{
					waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
				}
			}
			
		}
		stage("upload war file to nexus"){
			steps {
				script{
					def pomVersionRead= readMavenPom file:'pom.xml'
					nexusArtifactUploader artifacts: 
						[
						[artifactId: 'springboot', 
						  classifier: '', 
						  file: 'target/Uber.jar', 
						  type: 'jar'
						 ]
						], 
						credentialsId: 'nexus', 
						groupId: 'com.example',
						nexusUrl: '15.206.146.219:8081', 
						nexusVersion: 'nexus3', 
						protocol: 'http', 
						repository: 'demo-release', 
						version: "${pomVersionRead.version}"
				}
			}
		}
	}
}
