
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
//     stage("Quality Gate"){
//       steps {
//         script {
//           timeout(time: 1, unit: 'HOURS') {
//             def qg = waitForQualityGate() 
//             if (qg.status != 'OK') {
//               error "Pipeline aborted due to quality gate failure: ${qg.status}"
// 	    }
//           }
//         }
//       }
//     }
    stage("Quality Gate Status Check") {
      steps {
	timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
	}
      }
    }
  }
}
