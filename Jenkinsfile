timestamps {

node () {
	stage ('Checkout') {
 	 checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/bnasslahsen/conjur-cicd-demo.git']]])
	}

	stage ('Clean ') {
 	 withMaven(maven: 'maven-3.8.4') {
 		 sh "mvn clean"
 		}
	}

	stage ('Compile and Test') {
 	 withMaven(maven: 'maven-3.8.4') {
 		 sh "mvn test"
 		}
	}

	stage ('Packge') {
 	 withMaven(maven: 'maven-3.8.4') {
 		 sh "mvn package -DskipTests"
 		}
	}

	stage ('Quality Analysis') {
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
		)]){
 	 withMaven(maven: 'maven-3.8.4') {
 		 sh "mvn sonar:sonar "
 		}
	}}

	stage ('Deploy to nexus') {
	withCredentials([
    		conjurSecretCredential(
    		credentialsId: 'nexus-hostname',
    		variable: 'NEXUS_HOSTNAME'
    		),
    		conjurSecretCredential(
    		credentialsId: 'nexus-username',
    		variable: 'NEXUS_USERNAME'
    		),
    		conjurSecretCredential(
    		credentialsId: 'nexus-token',
    		variable: 'NEXUS_TOKEN'
    		)]){
		 withMaven(maven: 'maven-3.8.4') {
     		 sh "mvn deploy -DskipTests"
     		}
    	}}



	stage ('Install to remote server') {
	withCredentials([
			conjurSecretCredential(
			credentialsId: 'deploy-server-username',
			variable: 'DEPLOY_SERVER_USERNAME'
			),
			conjurSecretCredential(
			credentialsId: 'deploy-server-hostname',
			variable: 'DEPLOY_SERVER_HOST'
			),
			conjurSecretCredential(
			credentialsId: 'deploy-server-keyfile-path',
			variable: 'DEPLOY_SERVER_KEY_PATH'
			)]){
	sh """
	scp -v -o StrictHostKeyChecking=no -i $DEPLOY_SERVER_KEY_PATH  /var/jenkins_home/workspace/conjur-cicd-demo/target/conjur-cicd-demo-1.15-SNAPSHOT.jar $DEPLOY_SERVER_USERNAME@$DEPLOY_SERVER_HOST:/home/devops
	 """
	}}
}
}
