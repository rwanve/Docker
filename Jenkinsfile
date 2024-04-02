pipeline {

  agent any 
  
  options {
      timeout(time: 15, unit: 'MINUTES')
  }

  parameters {
     string(name: 'ECRURL', defaultValue: '654654501193.dkr.ecr.us-east-1.amazonaws.com', description: 'Please Enter your Docker ECR REGISTRY URL?')
     string(name: 'REPO', defaultValue: 'wezvabaseimage', description: 'Please Enter your Docker Repo Name?')
     string(name: 'REGION', defaultValue: 'us-east-1', description: 'Please Enter your AWS Region?')
  }

 stages  {
  stage('Checkout')
  {

   steps { 
    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rwanve/Docker.git']])
   }
  }
  
  stage('Run Linter')
  {
 
   steps { 
    sh "docker run --rm -i hadolint/hadolint < Dockerfile | tee -a dockerlinter.log"
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

             /* Push the Image to the Registry */
             myImage.push()
          }
      }
    }
  }
  
  stage('Explore Image Layers')
  {
   
   steps { 
     script {
	      //Prepare the Tag name for the Image
	     env.dockerTag = params.REPO + ":" + env.BUILD_ID

       sh "docker run --rm -i \
         -v /var/run/docker.sock:/var/run/docker.sock \
         wagoodman/dive:latest --ci ${dockerTag} --lowestEfficiency=0.8 --highestUserWastedPercent=0.45"
    }
   } 
  }

  stage ('Quality Gates')
  {
    
	steps {
	  withAWS(credentials:'AWSCred') {
	   sh "./getimagescan.sh ${params.REPO} ${env.BUILD_ID} ${params.REGION}"
	  }
	}
	post {
     always {
	  sh "docker rmi ${params.REPO}:${env.BUILD_ID}"
	 }
  }
  }
  
 }
}
