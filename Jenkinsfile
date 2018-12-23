 node{
   try{
	stage('SCM Checkout'){
     git 'https://github.com/octopent/my-app'
   }
   stage('Compile-Package'){
      // Get maven home path
      def mvnHome =  tool name: 'jenkins_maven', type: 'maven'   
      sh "${mvnHome}/bin/mvn package"
   }
   
   stage('SonarQube Analysis') {
        def mvnHome =  tool name: 'jenkins_maven', type: 'maven'
        withSonarQubeEnv('jenkins_sonar') { 
          sh "${mvnHome}/bin/mvn sonar:sonar"
        }
    }
    
    stage("Quality Gate Statuc Check"){
          timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                   //slackSend baseUrl: 'https://hooks.slack.com/services/',
                   //channel: '#jenkins-pipeline-demo',
                   //color: 'danger', 
                   //message: 'SonarQube Analysis Failed', 
                   //teamDomain: 'javahomecloud',
                   //tokenCredentialId: 'slack-demo'
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
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
		
		//ertyu
		currentBuild.result = 'FAILURE'
		
   }
}
