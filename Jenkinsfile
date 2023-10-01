pipeline {
    agent any
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        GCLOUD_KEY = credentials('gcp-key')
        GOOGLE_APPLICATION_CREDENTIALS = "${GCLOUD_KEY}"
    }
    
    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vedharagavan2404/webapp-gke.git'
            }
        }

        stage('Build Docker Image for SQLDB') {
            steps {
                script {
                    sh '''
                    echo 'Build SqlDb Image'
                    docker build -t us-east1-docker.pkg.dev/kubernetes-app-398819/database-image/sqldb:${BUILD_NUMBER} -f dockerfile_mysql .
                    '''
                }
            }
        }

        stage('Push the artifacts to GCP Artifact Registry') {
            steps {
                script {
                    sh '''
                    echo 'Push Database image to GCP Artifact Registry'
                    gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"
                    gcloud auth configure-docker us-east1-docker.pkg.dev --quiet
                    docker push us-east1-docker.pkg.dev/kubernetes-app-398819/database-image/sqldb:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
}
