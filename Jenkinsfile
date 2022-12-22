
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
      }
    }
    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('SonarQube') { 
	  sh "mvn sonar:sonar"
	}
      }
    }
//     stage("Nexus Artifact Upload") {
//       steps {
//         nexusArtifactUploader artifacts: [[artifactId: 'DevOpsDemo', 
// 		classifier: '', 
// 		file: 'target/DevOpsDemo.war', 
// 		type: 'war']], 
// 		credentialsId: 'nexus', 
// 		groupId: 'DevOpsDemo', 
// 		nexusUrl: '43.204.141.196:8081', 
// 		nexusVersion: 'nexus3', 
// 		protocol: 'http', 
// 		repository: 'maven-snapshots', 
// 		version: '1.0-SNAPSHOT'
//       }
//     }
    stage("Quality Gate Status Check") {
      steps {
	timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
	}
      }
      post {
	success {
	  slackSend message:"Build succeeded with Quality Gate success  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
	}
	failure {
	  slackSend message:"Build failed due to Quality Gate failure  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
	}
      }
    }
    stage("deploy"){
      steps{
	sshagent(['tomcat-new']) {
	  sh """
	    wget --user=admin --password=nexus http://65.2.70.211:8081/repository/maven-snapshots/DevOpsDemo%2FDevOpsDemo%2F1.0-SNAPSHOT%2FDevOpsDemo-1.0-20221221.140510-3.war
	    mv DevOpsDemo%2FDevOpsDemo%2F1.0-SNAPSHOT%2FDevOpsDemo-1.0-20221221.140510-3.war DevOpsDemo.war
	    scp -o StrictHostKeyChecking=no DevOpsDemo.war ubuntu@172.31.6.47:/home/ubuntu
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
