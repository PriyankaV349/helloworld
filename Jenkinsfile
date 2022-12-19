pipeline{
    agent any
    
    environment{
        PATH = "$PATH:/usr/share/maven/bin"
    }
    stages{
        stage("Git Checkout"){
            steps{
                git url: 'https://github.com/PriyankaV349/helloworld.git'
            }
        }
        stage("Maven Build"){
            steps{
                sh "mvn clean package"
                sh "mv target/*.jar target/helloworld-1.0.jar"
            }
        }
        stage("deploy"){
            steps{
                sshagent(['tomcat']) {
                sh """
                    scp -o StrictHostKeyChecking=no target/helloworld-1.0.jar  ec2-user@172.31.5.176:/opt/tomcat/webapps/
                    
                    ssh ec2-user@172.31.5.176 /opt/tomcat/bin/shutdown.sh
                    
                    ssh ec2-user@172.31.5.176 /opt/tomcat/bin/startup.sh
                
                """
                }
            
            }
        }
    }
}
