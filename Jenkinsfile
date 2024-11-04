pipeline {
agent{
    kubernetes{
       inheritFrom 'jenkins-slave'
    }
}
    environment {
        ZONE = "europe-west1-b"
        PROJECT_ID = "pod-x-sarjan-purohit"
    }

    stages{
        
        stage('Checkout') {
            steps {
                // Checkout code from the public Git repository
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],  // Replace 'main' with your branch name
                          userRemoteConfigs: [[url: 'https://github.com/agam-bajwa/gcp-test.git']]])
                          
            }
        }
        stage("Building Application Docker Image"){
            steps{
                script{
                    sh 'gcloud auth configure-docker europe-west1-docker.pkg.dev'
                    sh 'docker build . -t europe-west1-docker.pkg.dev/pod-x-sarjan-purohit/jenkins/hello-app:${BUILD_NUMBER}'
                    }
                }
            }
            
        stage('Update Deployment YAML') {
            steps {
                script {
                    sh '''
                    sed -i "s|{batman}|${BUILD_NUMBER}|g" deployment.yaml
                    '''
                }
            }
        }
        
        stage("Pushing Application Docker Image to Google Artifact Registry"){
            steps{
                script{
                    sh 'docker push europe-west1-docker.pkg.dev/pod-x-sarjan-purohit/jenkins/hello-app:${BUILD_NUMBER}'
        }}}
        stage("Application Deployment on Google Kubernetes Engine"){
            steps{
                script{
                    sh "gcloud container clusters get-credentials jenkins-cluster --zone ${env.ZONE} --project ${env.PROJECT_ID}"
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
    }
