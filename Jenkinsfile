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
             nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], credentialsId: 'nexus-credentials', groupId: 'SampleWebApp', nexusUrl: '54.198.178.56:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0.SNAPSHOT'
              }

        }
        stage('Deploy to tomcat') {
           steps {
              deploy adapters: [tomcat9(credentialsId: 'tomcat-crd', path: '', url: 'http://3.219.41.155:8080/')], contextPath: 'web', war: '**/*.war'
                }
        }
    }
}
