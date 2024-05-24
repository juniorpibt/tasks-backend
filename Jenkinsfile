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
			enviroment {
				scanner = tool 'SONAR_SCANNER'
			}
			withSonarQubeEnv('SONAR'){
				sh "${scanner}/bin/sonar-scanner -e -Dsonar.host.url=http://172.17.0.1:9000 -Dsonar.projectKey=Backend -Dsonar.java.binaries=target -Dsonar.exclusions=src/test/**"
			}
		}
	}
}