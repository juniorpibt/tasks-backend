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
		stage('BuildImage'){
			steps {
				sh 'docker build -t backend .'
			}
		}
		stage('Clean'){
			steps {
				catchError(buildResult: 'SUCCESS') {
					sh 'docker stop tasks'
				}
				
			}
		}
		stage ('Deploy'){
			steps {
				sh 'docker run -d --rm --name tasks -e DATABASE_HOST=172.17.0.1 -p 8001:8001 backend'
			}
		}
		stage('Check'){
			steps {
				sleep 5
				sh 'curl -I http://172.17.0.1:8001/todo'
			}
		}
	}
}
