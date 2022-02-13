// Powered by Infostretch 

timestamps {

node () {

	stage ('conjur-cicd-demo - Checkout') {
 	 checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/bnasslahsen/conjur-cicd-demo.git']]]) 
	}
	stage ('conjur-cicd-demo - Build') {
 	
// Unable to convert a build step referring to "org.jenkinsci.plugins.credentialsbinding.impl.SecretBuildWrapper". Please verify and convert manually if required.		// Maven build step
	withMaven(maven: 'maven-3.8.4') { 
 			if(isUnix()) {
 				sh "mvn clean package " 
			} else { 
 				bat "mvn clean package " 
			} 
 		}		// Maven build step
	withMaven(maven: 'maven-3.8.4') { 
 			if(isUnix()) {
 				sh "mvn sonar:sonar " 
			} else { 
 				bat "mvn sonar:sonar " 
			} 
 		}		// Maven build step
	withMaven(maven: 'maven-3.8.4', mavenSettingsFilePath: '/var/jenkins_home/maven/settings.xml', globalMavenSettingsFilePath: '/var/jenkins_home/maven/settings.xml') { 
 			if(isUnix()) {
 				sh "mvn deploy -DskipTests " 
			} else { 
 				bat "mvn deploy -DskipTests " 
			} 
 		}		// Shell build step
sh """ 
scp -v -o StrictHostKeyChecking=no -i $DEPLOY_SERVER_KEY_PATH  /var/jenkins_home/workspace/conjur-cicd-demo/target/conjur-cicd-demo-1.15-SNAPSHOT.jar $DEPLOY_SERVER_USERNAME@$DEPLOY_SERVER_HOST:/home/devops 
 """ 
	}
}
}
