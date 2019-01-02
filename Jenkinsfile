node{
   try{
    	stage('GIT CHECKOUT'){
         git 'https://github.com/octopent/my-app'
        }
        
        stage('COMPILATION'){
          // Get maven home path
          def mvnHome =  tool name: 'jenkins_maven', type: 'maven'   
          sh "${mvnHome}/bin/mvn package"
        }
   
        stage('SONARQUBE ENVIRONMENT') {
            def mvnHome =  tool name: 'jenkins_maven', type: 'maven'
            withSonarQubeEnv('jenkins_sonar') { 
              sh "${mvnHome}/bin/mvn sonar:sonar"
            }
        }
        
        stage("QUALITY GATE"){
            timeout(time: 1, unit: 'HOURS') {
                def qg = waitForQualityGate()
                if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
            }
        }
        
        stage('ARTIFACT UPLOAD') {
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
	
    	stage('ARTIFACT DOWNLOAD')
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
    
    	stage('JIRA ENVIRONMENT'){
    
          if(currentBuild.previousBuild.result.toString() == 'SUCCESS'){
              
              //SENDING EMAIL
              stage('EMAIL NOTIFICATION'){
                    
                    mail bcc: '', body: '''Hi!
        		    Your Build Passed!
        		    All Good!
        		    Mayank''', cc: '', from: '', replyTo: '', subject: 'BUILD SUCCESS', to: 'rathore.mayanksgh@gmail.com'
              }
          }
          
          else{
                //MAKING A TRANSITION 
                stage('JIRA ISSUE TRANSITION'){
                          withEnv(['JIRA_SITE=jenkins_jirasteps']) {
                      def transitionInput = 
                      [
                          transition: [
                              id: '21'
                          ]
                      ]
                      jiraTransitionIssue idOrKey: 'MAYAN-1', input: transitionInput
                    }
                }
          }
       }


   }catch (err){
        if(currentBuild.previousBuild.result.toString() == 'SUCCESS'){
            //CREATING A NEW JIRA ISSUE
            stage('CREATTING NEW JIRA ISSUE'){

                withEnv(['JIRA_SITE=jenkins_jirasteps']) {
                def testIssue = [fields: [ project: [id: '10001'],
                                      summary: 'BUILD FAILED' ,
                                      description: 'BUILD FAILED FOR BUILD ' + env.BUILD_ID,
                                      issuetype: [name: 'Bug']]]
    
                response = jiraNewIssue issue: testIssue
                
                jiraAssignIssue idOrKey: response.data.key, userName: 'adityajain3896'

                echo response.successful.toString()
                echo response.data.toString()
                
                currentBuild.result = 'FAILURE'
                }
            }
        }
        else{
            //ADDING A JIRA COMMENT
            stage('ADDING A JIRA COMMENT'){
                withEnv(['JIRA_SITE=jenkins_jirasteps']) {
                    jiraAddComment idOrKey: 'MAYAN-4', comment: 'Some error!'
                }
            }
        }
    }
}
