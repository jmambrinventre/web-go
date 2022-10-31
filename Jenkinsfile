pipeline {
    agent { label 'tuto' }
    
    environment {
        DOCKERHUB_CREDS = credentials('25')
    }

    stages {
        
        stage('BuildOnee') {
            when {
                branch 'develop'
            }
            steps {
                container('podman') {
                    script {
                        sh 'podman build -t docker.io/jmambrinventre/web-go:$BUILD_NUMBER -f Dockerfile'
                        sh 'podman login docker.io -u $DOCKERHUB_CREDS_USR -p $DOCKERHUB_CREDS_PSW'
                        sh 'podman tag docker.io/jmambrinventre/web-go:$BUILD_NUMBER docker.io/jmambrinventre/web-go:develop'
                        sh 'podman push docker.io/jmambrinventre/web-go:$BUILD_NUMBER'
                        sh 'podman push docker.io/jmambrinventre/web-go:develop'
                    }
                }
            }
        }
        stage('BuildOne') {
            when {
                branch 'develop'
            }
            steps {
                container('kubectl') {
                    script {
                        sh 'kubectl apply -f desa.yaml'
                        sh 'kubectl rollout restart deployment -n web-go-desa web-go'
                        sh 'kubectl rollout status deployment -n web-go-desa web-go'
                    }
                }
            }
        }
            
        stage('Build') {
            when {
                branch 'main'
            }
            steps {
                container('podman') {
                    script {
                        sh 'podman login docker.io -u $DOCKERHUB_CREDS_USR -p $DOCKERHUB_CREDS_PSW'
                        sh 'podman pull docker.io/jmambrinventre/web-go:develop'                       
                        sh 'podman tag docker.io/jmambrinventre/web-go:develop docker.io/jmambrinventre/web-go:latest'
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
