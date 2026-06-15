pipeline {
    agent any

    environment {
        GKE_CLUSTER_NAME = 'cluster-1' // GKE cluster name
        GKE_PROJECT_ID = 'kiran400' // GCP project ID
        GKE_ZONE = 'us-central1' // GKE zone
        GIT_REPO_URL = 'https://github.com/praveen-goud999/k8s-app.git'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Checkout Git Repository') {
            steps {
                script {
                    git url: "${GIT_REPO_URL}", branch: "${GIT_BRANCH}"
                }
            }
        }

        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    script {
                        // Activate service account securely
                        sh 'gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'
                        sh 'gcloud config set project "$GKE_PROJECT_ID"'
                        sh 'gcloud config set compute/zone "$GKE_ZONE"'
                    }
                }
            }
        }

        stage('Configure kubectl') {
            steps {
                script {
                    sh 'gcloud container clusters get-credentials "$GKE_CLUSTER_NAME" --zone "$GKE_ZONE" --project "$GKE_PROJECT_ID"'
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                script {
                    // Ensure file path is correct relative to your repo
                    sh 'kubectl apply -f aks-store-quickstart.yaml'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
    }
}
