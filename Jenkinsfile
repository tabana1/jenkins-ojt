@Library('https://github.com/tabana1/java-shared-library') _

pipeline {
    agent any

    environment {
        dockerHubCredentialsID = 'docker'
        imageName = 'tabana1/static-website'
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        GIT_REPO = 'https://github.com/IbrahimAdell/Lab.git'
        OPENSHIFT_PROJECT = 'my-devops-tools'
        OPENSHIFT_CREDENTIALS_ID = 'open-shift-service'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${env.GIT_REPO}"
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running Unit Test..."
                    // Call a function from java-shared-library/vars
                    runUnitTests()
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Call another function from java-shared-library/vars
                    dockerBuildAndPush(
                       dockerHubCredentialsID, imageName
                    )
                }
            }
        }

        stage('Deploy to OpenShift') {
            steps {
                script {
                    // Call another function from java-shared-library/vars
                    openshiftDeploy(OPENSHIFT_CREDENTIALS_ID, imageName)
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
