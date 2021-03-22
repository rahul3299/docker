pipeline {
    agent any 
    environment{
        dockerImage=''
        registry='rahul3299/docker-test'
        registryCredential="Docker"
    }
    stages {
        stage('Checkout') { 
            steps {
                git 'https://github.com/rahul3299/docker.git'
            }
        }
        stage('Build') { 
            steps {
                bat "mvn clean install" 
            }
        }
        stage("Artifactory"){
            steps{
                rtMavenDeployer(
                    id: 'deployer',
                    serverId: 'Artifact',
                    releaseRepo:'example-repo-local',
                    snapshotRepo:'example-repo-local')
                rtMavenRun(
                    pom:'pom.xml',
                    goals:'clean install',
                    deployerId:'deployer')
                rtPublishBuildInfo(
                    serverId:'Artifact')
            }
        }
        stage('Build Docker Image')
           {
               steps
               {
                   script{
                       dockerImage = docker.build registry
                   }
               }
           }
            stage('Uploading Image')
           {
               steps
               {
                   script{
                       docker.withRegistry( '',registryCredential ) {
                           dockerImage.push()
                       }
                   }
               }
           }
            stage('Docker Run')
           {
               steps
               {
                   script{
                       dockerImage.run("-p 5000:8096 --rm --name docker-testContainer")
                   }
                  
               }
           }
    }
}
