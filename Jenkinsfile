@Library('java-shared-library') _
pipeline {

    agent {  }
    
    environment {
        dockerHubCredentialsID	    = 'docker'  		    			// DockerHub credentials ID.
        imageName   		    = 'tabana1/static-website'     			// DockerHub repo/image name.
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        OPENSHIFT_PROJECT = 'my-devops-tools'
       OPENSHIFT_TOKEN = credentials('open-shift-service')     }
    


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
	
       
        stage('Build Docker Image') {
            steps {
                script {
        		echo "Building docker image ..."
        		sh "docker build -t ${imageName}:${BUILD_NUMBER} ."
                }
            }
        }

       
        stage('push Docker Image') {
            steps {
                script {
        		echo "pushing docker image ..."
			withCredentials([usernamePassword(credentialsId: "${dockerHubCredentialsID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
				sh "docker login -u ${USERNAME} -p ${PASSWORD}"
        		}
			sh "docker push ${imageName}:${BUILD_NUMBER}"
                }
            }
        }
	    
	stage('Deploy on OpenShift cluster') {
            steps {
                script {
        		// Log in to the OpenShift cluster and deploy the image
                    sh """
                        oc login ${OPENSHIFT_SERVER} --token=${SERVICE_ACCOUNT_TOKEN}
                        oc project ${OPENSHIFT_PROJECT}
                        
                        oc set image deployment/your-deployment-name your-container-name=${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

    }

    post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
}