pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "m3"
    }

    stages {
        stage('pull from scm') {
            steps{
            git credentialsId: 'git-chat-cred', url: 'https://github.com/gopal1409/springchat1.git'
            }
        }
        stage('mvn build the app') {
            steps{
            sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
            post {
                //if maven build was able to run the test we will create a test report and archive the jar in local machine
                success {
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }}
        }
        stage('checkstyle') {
            steps{
            sh "mvn checkstyle:checkstyle"
            }}
            
        stage('checkstyle Report') {
            steps {
                recordIssues(tools: [checkStyle(pattern: 'target/checkstyle-result.xml')])
            }
        }
        
        stage('code coverage') {
            steps {
                jacoco()
            }
        }
        
        stage('code quality check sonar') {
            
            steps {
                
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=chatapp -Dsonar.host.url=http://20.84.83.13:9000 -Dsonar.login=sqp_264fd44c623ac0dd195e16a8503e0f88838669b5'
            }
            
        }
        
        stage ('Nexus upload')  {
          steps {
          nexusArtifactUploader(
          nexusVersion: 'nexus3',
          protocol: 'http',
          nexusUrl: '20.115.61.38:8081',
          groupId: 'websocket-demo',
          version: '0.0.1-SNAPSHOT',
          repository: 'maven-snapshots',
          credentialsId: 'nexus-cred',
          artifacts: [
            [artifactId: 'websocket-demo',
             classifier: '',
             file: 'target/websocket-demo-0.0.1-SNAPSHOT.jar',
             type: 'jar']
        ]
        )
          }
     }
        
    }}
