pipeline {
    
    agent any 
    // agent is where my pipeline will be eexecuted
    tools {
        //install the maven version configured as m2 and add it to the path
        maven "m3"
    }
    stages {
        stage('pull from scm') {
            steps {
            git credentialsId: 'git-chat-cred', url: 'https://github.com/gopal1409/springchat1.git'
            }
        }
         stage('build it') {
            steps {
            sh 'mvn clean package'
            }
        }
        
        stage('install tomcat using ansible'){
            steps {
            ansiblePlaybook(
                installation: 'ansible',
                credentialsId: 'ansible-jenkins',
                inventory: '/home/nitin/ansible-tomcat/client.inv',
                playbook: '/home/nitin/ansible-tomcat/tomcat.yml')
    
        }}
        
        stage("deploy-jar"){
            steps{
            sshagent(['ansible-jenkins']) {
               sh """
               scp -o StrictHostKeyChecking=no target/websocket-demo-0.0.1-SNAPSHOT.jar root@client:/usr/share/tomcat/webapps
               ssh root@client /usr/share/tomcat/bin/shutdown.sh
               ssh root@client /usr/share/tomcat/bin/startup.sh
               """
           }
      }
}
        
        
    }
}
