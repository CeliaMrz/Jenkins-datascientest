pipeline { 
    agent any
    environment { 
        DOCKER_ID = "dstdockerhub"
        DOCKER_IMAGE = "datascientestapi"
        DOCKER_TAG = "v.${BUILD_ID}.0" 
    }
    stages {
        stage('Building') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Testing') {
            steps {
                sh 'python3 -m unittest discover'  // Utilisation de python3 au lieu de python
            }
        }
        stage('Deploying') {
            steps {
                script {
                    sh '''
                    docker rm -f jenkins || true
                    docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                    docker run -d -p 8000:8000 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }
        stage('User acceptance') {
            steps {
                input message: "Proceed to push to main", ok: "Yes"
            }
        }
        stage('Pushing and Merging') {
            parallel {
                stage('Pushing Image') {
    steps {
        script {
            // Use DockerHub credentials
            docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                sh 'docker push dstdockerhub/datascientestapi:v.14.0'
            }
        }
    }
}
                stage('Merging') {
                    steps {
                        echo 'Merging done'
                    }
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
