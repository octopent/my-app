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
    
    stage("Quality Gate Static Check"){
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
      stage('Artifact upload') {
    def server = Artifactory.server 'jenkins_artifactory'
   	def uploadSpec = """{
     "files": [
           {
              "pattern": "/var/lib/jenkins/workspace/artifactory-dep-project/target/*.war",
              "target": "mayank-snapshot"
           }
        ]
        }"""
    server.upload(uploadSpec)
	}
	
	stage('downloading artifact')
    {
        def server = Artifactory.server 'jenkins_artifactory'
        def downloadSpec="""{
        "files":[
        {
           "pattern":"mayank-snapshot/z12345.war",
            "target":"/var/lib/jenkins/warFiles/"
        }
        ]
        }"""
    server.download(downloadSpec)
    }
    
    //stage ('Final deploy'){
        //sh 'scp /var/lib/jenkins/warFiles/z12345.war minduseradmin@mnevm41269dns.WestUS.cloudapp.azure.com:/opt/tomcat/webapps/'
    //}
	stage('Email Notification'){
		mail bcc: '', body: '''Hi!
		Your Build Passed!
		All Good!
		Mayank''', cc: '', from: '', replyTo: '', subject: 'BUILD SUCCESS', to: 'rathore.mayanksgh@gmail.com'
	}
   }catch (err){
		mail bcc: '', body: '''Hi! 
		Oops! Build Failed!
		Mayank''', cc: '', from: '', replyTo: '', subject: 'BUILD FAILED', to: 'rathore.mayanksgh@gmail.com'
		
		//ertyu
		currentBuild.result = 'FAILURE'
		
   }
}
