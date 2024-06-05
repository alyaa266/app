pipeline {
    agent any
    environment {
        openshiftClusterURL  = 'https://api.ocp-training.ivolve-test.com:6443'
        OPENSHIFT_CREDENTIALS = 'openshift-jenkins-token'
        dockerHubCredentialsID	    = 'dockerhub-credentials'
        openshiftCredentialsID =  'openshift-jenkins--token'
        APP_NAME = 'my-app'
        NAMESPACE = 'alyaabadr'
    }
    stages {
       
      
        stage('Build and push Docker Image') {
            steps {
                script {
                    // Define the image tag
                    def imageTag = "${env.IMAGE_REGISTRY}/${env.APP_NAME}:${env.BUILD_NUMBER}"
                    
                    // Build the Docker image
                    sh "docker build -t ${imageTag} ."
  withCredentials([usernamePassword(credentialsId: "${dockerhub-credentials}", usernameVariable: 'USERNAME',             passwordVariable: 'PASSWORD')]) 
 {
		sh "docker login -u ${USERNAME} -p ${PASSWORD}"
}
                    
                    // Push the Docker image to the registry
                    sh "docker push ${imageTag}"
                    
                    // Set the image tag as an environment variable for later use
                    env.IMAGE_TAG = imageTag
                }
            }
        }
      
        stage('Deploy to OpenShift') {
            steps {
                script {
                   sh "sed -i 's|image:.*|image: ${imageName}:${BUILD_NUMBER}|g' deployment.yml"

    // login to OpenShift Cluster via cluster url & service account token
    withCredentials([string(credentialsId: "${openshift-jenkins--token}", variable: 'OpenShift_CREDENTIALS')]) {
            sh "oc login --server=https://api.ocp-training.ivolve-test.com:6443 --token=${openshift-jenkins--token} --insecure- skip-tls-verify"
            sh "oc apply -f deployment.yml"
            sh "oc apply -f service.yml"
                        }
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

