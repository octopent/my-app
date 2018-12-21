  node{
   stage('SCM Checkout'){
     git 'https://github.com/octopent/my-app'
   }
   stage('Compile-Package'){
      def mvnHome =  tool name: 'jenkins-maven', type: 'maven'   
      sh "${mvnHome}/bin/mvn package"
   }
   stage('Email Notification'){
      mail bcc: '', body: '''Hi Welcome to jenkins email alerts
      Thanks
      Hari''', cc: '', from: '', replyTo: '', subject: 'Jenkins Job', to: 'rathore.mayanksgh@gmail.com'
   }
}


