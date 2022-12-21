
pipeline {
  agent any
    
  environment {
    PATH = "$PATH:/usr/share/maven/bin"
  }
  stages {
    stage("Git Checkout") {
      steps {
	git url: 'https://github.com/PriyankaV349/DevOpsDemo.git'
      }
    }
    stage("Maven Build") {
      steps {
        sh "mvn clean package"
	//sh "mv target/*.war target/DevOpsDemo.war"
      }
    }
    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('sonarqube-8.9.1.44547') { 
	  sh "mvn sonar:sonar"
	}
      }
    }
//     stage("Quality Gate Status Check") {
//       steps {
// 	timeout(time: 1, unit: 'HOURS') {
//           waitForQualityGate abortPipeline: true
// 	}
//       }
//       post {
// 	success {
// 	  slackSend message:"Build succeeded with Quality Gate success  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
// 	}
// 	failure {
// 	  slackSend message:"Build failed due to Quality Gate failure  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
// 	}
//       }
//     }
    stage("deploy"){
      steps{
	sshagent(['tomcat-new']) {
	  sh """
	    scp -o StrictHostKeyChecking=no target/DevOpsDemo.war ubuntu@172.31.6.47:/home/ubuntu
	    ssh -o StrictHostKeyChecking=no ubuntu@172.31.6.47 'sudo cp -r /home/ubuntu/DevOpsDemo.war /opt/tomcat/webapps/'                   
	    ssh ubuntu@172.31.6.47 sudo /opt/tomcat/bin/shutdown.sh                    
	    ssh ubuntu@172.31.6.47 sudo /opt/tomcat/bin/startup.sh                
	  """
	}            
      }
    }
  }
  post {
    success {
      slackSend message:"Build deployed successfully - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
    } 
    failure {
      slackSend message:"Build failed  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
    }	
  }
}
