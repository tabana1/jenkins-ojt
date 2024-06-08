@Library('java-shared-library') _
pipeline {

    agent {  }
    
    environment {
        dockerHubCredentialsID	    = 'docker'  		    			// DockerHub credentials ID.
        imageName   		    = 'tabana1/static-website'     			// DockerHub repo/image name.
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        GIT_REPO = 'https://github.com/IbrahimAdell/Lab.git'

        OPENSHIFT_PROJECT = 'my-devops-tools'
       OPENSHIFT_TOKEN = credentials('open-shift-service')     
}
    

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${env.GIT_REPO}"
            }
        }

    stages {       

        stage('Test') {
            steps {
                script {
                	echo "Running Unit Test..."
			sh 'chmod 744 gradlew'
			sh './gradlew clean test'
        	}
    	    }
	}
	
       
        stage('Build and Push Docker Image') {
            steps {
                script {
                    buildAndPushDockerImage(
                        dockerHubCredentialsID: "${env.DOCKER_HUB_CREDENTIALS_ID}",
                        imageName: "${env.REGISTRY}/${env.IMAGE_NAME}"
                    )
                }
            }
        }
	    
        stage('Deploy to OpenShift') {
            steps {
                script {
                    deployToOpenshift(
                        openshiftCredentialsID: "${env.OPENSHIFT_CREDENTIALS_ID}",
                        imageName: "${env.REGISTRY}/${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
                    )
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline execution completed'
        }
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline execution failed'
        }
    }
}