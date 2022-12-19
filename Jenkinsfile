pipeline{
    agent any
    
    environment{
        PATH = "$PATH:/usr/share/maven/bin"
    }
    stages{
        stage("Git Checkout"){
            steps{
                git url: 'https://github.com/PriyankaV349/DevOpsDemo.git'
            }
        }
        stage("Maven Build"){
            steps{
                sh "mvn clean package"
                sh "mv target/*.war target/DevOpsDemo.war"
            }
        }
        stage("deploy"){
            steps{
                sshagent(['tomcat-new']) {
                sh """
		    scp -o StrictHostKeyChecking=no target/*.war ubuntu@172.31.6.47:/home/ubuntu

		    ssh -o StrictHostKeyChecking=no ubuntu@172.31.6.47 'sudo cp -r /home/ubuntu/DevOpsDemo.war /opt/tomcat/webapps/'
                    
                    ssh ubuntu@172.31.6.47 sudo /opt/tomcat/bin/shutdown.sh
                    
                    ssh ubuntu@172.31.6.47 sudo /opt/tomcat/bin/startup.sh
                
                """
                }
            
            }
        }
    }
}
