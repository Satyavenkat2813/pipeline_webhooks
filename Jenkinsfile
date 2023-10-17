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
	}
}
