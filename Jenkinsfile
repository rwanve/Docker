pipeline {

  agent any 
  
  options {
      timeout(time: 15, unit: 'MINUTES')
  }

  parameters {
     string(name: 'ECRURL', defaultValue: '654654501193.dkr.ecr.us-east-1.amazonaws.com', description: 'Please Enter your Docker ECR REGISTRY URL?')
     string(name: 'REPO', defaultValue: 'wezvabaseimage', description: 'Please Enter your Docker Repo Name?')
     string(name: 'REGION', defaultValue: 'ap-south-1', description: 'Please Enter your AWS Region?')
  }

 stages  {
  stage('Checkout')
  {
   steps { 
    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rwanve/Docker.git']])
   }
  }
  
  stage('Build Image') 
  {
 
    steps{
      script {
	      //Prepare the Tag name for the Image
	      dockerTag = params.REPO + ":" + env.BUILD_ID
		  
          docker.withRegistry( params.ECRURL, 'ecr:us-east-1:AWSCred' ) {
             /* Build Docker Image locally */
             myImage = docker.build(dockerTag)

          }
      }
    }
