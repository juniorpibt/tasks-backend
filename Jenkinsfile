pipeline {
	agent any
	tools {
		maven 'maven'
	}
	stages {
		stage('Build'){
			steps {
				sh '''
				mvn clean package -DskipTests
				'''
			}
		}
		stage('Test'){
			steps {
				sh 'mvn test'
			}
		}
		stage('Sonar'){
			environment {
				scanner = tool 'SONAR_SCANNER'
			}
			steps {
				withSonarQubeEnv('SONAR'){
					sh "${scanner}/bin/sonar-scanner -e -Dsonar.host.url=http://172.17.0.1:9000 -Dsonar.projectKey=backend -Dsonar.java.binaries=target -Dsonar.exclusions=src/test/**"
				}	
			}
		}
		stage('QualityGate'){
			steps {
				sleep(10)
				timeout(1){
					waitForQualityGate abortPipeline: true, credentialsId: 'TOKEN_SONAR'
				}
			}
		}
	}
}
