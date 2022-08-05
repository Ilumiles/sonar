pipeline {
    agent any
    tools {
    maven 'Maven' 
       }
    stages {
        stage('Git checkout') {
            steps {
                git 'https://github.com/Ilumiles/sonar.git'
            }
        }
        
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn clean install'
            }
        }
        
             stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        
            }
        stage('Code Qualty Scan') {

           steps {
                  withSonarQubeEnv('sonarserver') {
             sh "mvn -f SampleWebApp/pom.xml sonar:sonar"      
               }
            }
       }
        stage('Quality Gate') {
          steps {
                 waitForQualityGate abortPipeline: true
              }
        }
        
        stage('push to Nexus') {
          steps {    
             nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], credentialsId: 'nexus', groupId: 'SampleWebApp', nexusUrl: '34.229.20.105:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0.0'
          }

        }
        stage('Deploy to tomcat') {
           steps {
              deploy adapters: [tomcat9(path: '', url: 'http://3.94.153.114:8080')], contextPath: 'web', war: '**/*.war'
           }
        }
    }
}
