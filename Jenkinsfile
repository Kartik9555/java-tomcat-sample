pipeline {
    agent any
    stages {
        stage('Build Application') {
            steps {
                bat 'mvn clean package verify sonar:sonar'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
		stage('Sonarqube') {
			environment {
				scannerHome = tool 'SonarQubeScanner'
			}
			steps {
				withSonarQubeEnv('sonarqube') {
					bat "${scannerHome}/bin/sonar-scanner"
				}	
				timeout(time: 10, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: true
				}
			}
		}			
        stage('Deploy in Staging Environment'){
            steps{
                build job: 'Deploy_Package_app'

            }
            
        }
        stage('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'
                }
                build job: 'Deploy_Package_app_prod'
            }
        }
    }
}