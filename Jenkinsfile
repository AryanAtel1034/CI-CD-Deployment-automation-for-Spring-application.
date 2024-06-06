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
            
            steps{

                script{
                def readPom= readMavenPom file: 'pom.xml'

                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot',
                            classifier: '',
                            file: 'target/Uber.jar',
                            type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '34.227.125.171:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http',
                    repository: 'DemoAppRelease', 
                    version: "${readPom.version}"
                }
               
                
            }
        }
        stage("Docker Image Build"){
            steps{
                sh 'docker image build -t $JOB_NAME.toLowerCase():v1.$BUILD_ID'
                sh 'docker images tag $JOB_NAME.toLowerCase():v1.$BUILD_ID aryanatel1034/$JOB_NAME.toLowerCase():v1.$BUILD_ID' 
                sh 'docker images tag $JOB_NAME.toLowerCase():v1.$BUILD_ID aryanatel1034/$JOB_NAME.toLowerCase():latest' 
            }
        }

         stage("Push Image to DockerHub"){
            steps{
                withCredentials([string(credentialsId: 'DockerHubPasswd', variable: 'passwd')]) {
                    sh 'docker login -u aryanatel1034 -p $passwd'
                }
                sh 'docker push aryanatel1034/newbuild:$JOB_NAME.toLowerCase():v1.$BUILD_ID'
            }
        }
       
       
    }
}
