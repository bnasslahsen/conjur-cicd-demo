timestamps {

node () {

	stage ('conjur-cicd-demo - Checkout') {
 	 checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/bnasslahsen/conjur-cicd-demo.git']]]) 
	}
	
	stage ('conjur-cicd-demo - Clean ') {
 	 withMaven(maven: 'maven-3.8.4') { 
 		 sh "mvn clean " 
 		}
	}
	
	stage ('conjur-cicd-demo -  Test') {
 	 withMaven(maven: 'maven-3.8.4') { 
 		 sh "mvn test " 
 		}
	}
	
	stage ('conjur-cicd-demo - Packge') {
 	 withMaven(maven: 'maven-3.8.4') { 
 		 sh "mvn package -DskipTests " 
 		}
	}
	
	stage ('conjur-cicd-demo - Quality Analysis') {
	withCredentials([
		conjurSecretCredential(
		credentialsId: 'sonar-token',
		variable: 'SONAR_TOKEN'
		),
		conjurSecretCredential(
		credentialsId: 'sonar-hostname',
		variable: 'SONAR_HOSTNAME'
		),
		conjurSecretCredential(
		credentialsId: 'sonar-organisation',
		variable: 'SONAR_ORGANISATION'
		),
		conjurSecretCredential(
		credentialsId: 'OPENWEATHER_API_KEY',
		variable: 'OPENWEATHER_API_KEY')
					       ])
 	 withMaven(maven: 'maven-3.8.4') { 
 		 sh "mvn sonar:sonar " 
 		}
	}
	
	stage ('conjur-cicd-demo - Deploy to nexus') {
	withMaven(maven: 'maven-3.8.4', mavenSettingsFilePath: '/var/jenkins_home/maven/settings.xml', globalMavenSettingsFilePath: '/var/jenkins_home/maven/settings.xml') { 
 		sh "mvn deploy -DskipTests " 
 		}
	}
			
	stage ('conjur-cicd-demo - Install in remote server') {	
	sh """ 
	scp -v -o StrictHostKeyChecking=no -i $DEPLOY_SERVER_KEY_PATH  /var/jenkins_home/workspace/conjur-cicd-demo/target/conjur-cicd-demo-1.15-SNAPSHOT.jar $DEPLOY_SERVER_USERNAME@$DEPLOY_SERVER_HOST:/home/devops 
	 """ 
	}
}
}
