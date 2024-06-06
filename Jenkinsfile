@Library('java-shared-library') _
pipeline {

    agent {  }
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'tabana1/java-app:latest'     			// DockerHub repo/image name.
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        OPENSHIFT_PROJECT = 'my-project'
        SERVICE_ACCOUNT_TOKEN = credentials('eyJhbGciOiJSUzI1NiIsImtpZCI6IjRnTW5ETnRFMXQzWFExOHdETzdGNUFsRUI4T3c1TGJIaVNkOWZuUzZiXzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJteS1wcm9qZWN0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InNsYXZlLXN2YS10b2tlbi1qNzVjNyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJzbGF2ZS1zdmEiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJkNWEyZGYyNS01NzRlLTRjZDktYTRiMy1jODE4ZDI5OTMwYzAiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6bXktcHJvamVjdDpzbGF2ZS1zdmEifQ.rStGzRLcIdxS-rS34x4PWpqruU1Hu0U9AnaV3rA3CiJaB9Y24VnnoCJgukUsL5IeByBpXIa0mNfGgok1v_YCQVlmj3SVRF5cMqME9HApN-gRNrmOuyrYzc-O0pfNRBcipYFOUbAF7eEfxYDmI3jfLT_SnmtyFQ6o4r1Kg9_yiEHA18Xyyb2s0KjvU3DDenz6M6XfohgG2g4h_mYmH-l4see7kRosMd5qid-q6GcrRtIqoDAilhkQAZXwlxEYNseXX7lQaWJwOftFHZC1BX-73fgHzTDutYn9HH1-wqmVpwF5WUpIm8WzyXOBfyIteF56cJBl3HWCyTnzl35FerPMsV8t7T72VFaV1BvmeCwhlUaHi05OaPerGUXvoEb2EIv0fuPrUyW6m6DLpYTXPxOp-XR3cqeROvrShlRlb-4nLrgx80QzAWw32ppAMzWxaHhthNeB0h_mgEn3lu1Feq8ssFAPoc0kqwFJxAsr5QH6JYQdgfh7WoqOj0dkybwXdESCkFb6gjoqWxyVdvgnpunD81UYQVMIGygdqP4l4JLM6lqG0B0LZLh-HAEyzVrzv4wyPHR9GtadDi_3rQFM-4OanSPLGB-RVI8HKKjKx5dWeilVJ2KtdADaQWGH60l1Ls-JEcVtxeFjg2zbYEqcPJATFUFlPjShGLXYwPftew91u6w
')
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