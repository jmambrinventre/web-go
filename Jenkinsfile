pipeline {
    agent { label 'tuto' }
    
    environment {
        DOCKERHUB_CREDS = credentials('25')
    }

    stages {
        stage('Build') {
            when {
                branch 'develop'
            }
            steps {
                container('podman') {
                    script {
                        sh 'podman build -t docker.io/jmambrinventre/web-go:$BUILD_NUMBER -f Dockerfile'
                        sh 'podman login docker.io -u $DOCKERHUB_CREDS_USR -p $DOCKERHUB_CREDS_PSW'
                        sh 'podman tag docker.io/jmambrinventre/web-go:$BUILD_NUMBER docker.io/jmambrinventre/web-go:latest'
                        sh 'podman push docker.io/jmambrinventre/web-go:$BUILD_NUMBER'
                        sh 'podman push docker.io/jmambrinventre/web-go:latest'
                    }
                }
            }
        }
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                container('kubectl') {
                    script {
                        sh 'kubectl apply -f todo.yaml'
                        sh 'kubectl rollout restart deployment -n web-go web-go'
                        sh 'kubectl rollout status deployment -n web-go web-go'
                    }
                }
            }
        }
    }
}
