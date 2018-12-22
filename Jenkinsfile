 node{
   try{
	stage('SCM Checkout'){
		git 'https://github.com/octopent/my-app'
	}
	stage('Compile-Package'){
		def mvnHome =  tool name: 'jenkins-maven', type: 'maven'   
		sh "${mvnHome}/bin/mvn package"
	}
	stage('SonarQube analysis') {
	withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) {
            withSonarQubeEnv('jenkins_sonar') {
                sh 'mvn sonar:sonar'
        	}
	   }
    	}
	stage('Email Notification'){
		mail bcc: '', body: '''Hi Welcome to jenkins email alerts
		All Good!
		Mayank''', cc: '', from: '', replyTo: '', subject: 'Jenkins Pipeline Job', to: 'rathore.mayanksgh@gmail.com'
	}
   }catch (err){
		mail bcc: '', body: '''Hi Welcome to jenkins email alerts
		Build Failed
		Mayank''', cc: '', from: '', replyTo: '', subject: 'Jenkins Pipeline Job', to: 'rathore.mayanksgh@gmail.com'
		
		currentBuild.result = 'FAILURE'
   }
}
