pipeline {
    agent any
    stages {
        stage("Git Checkout") {
            steps {
                git branch: 'main', 
                changelog: false,
                 poll: false, 
                 url: 'https://github.com/Satyavenkat2813/pipeline_webhooks.git'
                
            }
        }
        stage("Maven Build") {
            steps {
               sh'mvn clean'
                
            }
        }
    }
}