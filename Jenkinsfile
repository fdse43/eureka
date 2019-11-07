pipeline {
    agent any
    environment {
      
	    GIT_URL = "https://github.com/wujiabo/eureka.git"
		DOCKER_REPO="registry.cn-hangzhou.aliyuncs.com/wujiabo/sba-registry"
		DOCKER_REG="https://registry.cn-hangzhou.aliyuncs.com"
		DOCKER_REG_KEY = "cddee4fd-275d-4a88-9e47-f980681f9d80"
		dockerImage = ''
      
    }
    stages {
    
    	stage('CheckOut Code'){
         	steps{
            	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GIT_URL]]])
            	}
              }
        stage('Maven Build'){
        	steps{
        	    bat 'mvn clean install -DskipTests'
        	}

        }

        stage('Building image') {
	      steps{
	        script {
	           docker.withRegistry( DOCKER_REG, DOCKER_REG_KEY ) {dockerImage = docker.build DOCKER_REPO + ":latest"
	           }
	        }
	      }
	    }
	    stage('Push Image') {
      steps{
        script {
		   docker.withRegistry( DOCKER_REG, DOCKER_REG_KEY ) {
		            dockerImage.push()
		          }
		        }
		      }
		}

		stage('Deploy Image to K8s') {
      steps{
        script {
	   		bat 'kubectl apply -f deployment.yaml'
		      }
		}
		}


		stage('Remove Unused docker image') {
      steps{
        bat "docker rmi $DOCKER_REPO:latest"
      }
    }
   }
  

}