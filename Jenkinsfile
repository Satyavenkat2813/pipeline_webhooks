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
		stage("	")
	}
}
