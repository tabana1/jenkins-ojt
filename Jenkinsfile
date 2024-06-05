@Library('java-shared-library') _
pipeline {

    //agent { label 'slave1' }
    // agent any 

    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'tabana1/java-app'     			// DockerHub repo/image name.
        openshiftcredintialsID  = 'openshift'
        OPENSHIFT_PROJECT = 'mohamedtabana'
    }
    


    stages {       

        stage('Test') {
            steps {
                script {
                    dir('Jenkins'){
                        dir('my-app'){   
                	    echo "Running Unit Test..."
			            // sh 'chmod 744 ./gradlew'
			            // sh './gradlew clean test'
                        }
                    }
        	    }
    	    }
	}
	
       
        stage('Build and Push Docker Image') {
            steps {
                script {
                	// Navigate to the directory contains Dockerfile
                 	dir('Jenkins') {
                        dir('app-master') {
                 		    buildandPushDockerImage("${dockerHubCredentialsID}", "${imageName}")
                    }
                }
            }
	    }
    }       
        
	stage('Deploy on OpenShift cluster') {
            steps {
                script {
        		// Navigate to the directory contains Deployment and Service YAML files
                    dir('Jenkins'){
                        dir('openshift'){
                            deployOnOpenShift("${openshiftcredintialsID}" , "${imageName}")
                        }
                    }

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