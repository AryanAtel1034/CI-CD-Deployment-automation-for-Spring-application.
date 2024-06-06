pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/AryanAtel1034/Project.git'
                }
            }
        }
        stage('Code Compile') {
            steps{
                
               
                    sh "mvn compile"

               
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('File System Scan (Trivy)') {
  
                steps{
                    sh "trivy fs --format table -o trivy-fs-report.html ."

                }
            
        }
        stage('Sonaqube Analysis ')  {
            // We have to configure sonar cube server on timestamp 1:08:15 in the video  
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId : 'sonar-token') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                }
                    
            }
        }
        
        stage('Quality Gate') {
            //  1:15:08 Webhook Integration must done 
            steps{
                
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'

                }
            }
        }
        stage('Build') {
            steps{
             
                sh "mvn package"

            }
        }
        stage('Publish to Nexus') {
            // TODO 1:17:09
            steps{
               //  // todo
               // withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true)
               //  {
    
               //  sh "mvn deploy"

                nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot',
                            classifier: '',
                            file: 'target/Uber.jar',
                            type: 'jar'
                        ]
                    ], credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '34.227.125.171:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http',
                    repository: 'DemoAppRelease', 
                    version: '1.0.0'
                
                }
            }
        }
        stage("Docker Image Build"){
            steps{
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID'
                sh 'docker images tag $JOB_NAME:v1.$BUILD_ID AryanAtel/$JOB_NAME:v1.$BUILD_ID' 
                 sh 'docker images tag $JOB_NAME:v1.$BUILD_ID AryanAtel/$JOB_NAME:latest' 
            }
        }
       
       
    }
}
