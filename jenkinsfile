pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sliferchris/assignment-2-app"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo "Cloning code..."
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Run Container to Test') {
            steps {
                sh '''
                    docker run -d --name test-container $DOCKER_IMAGE:$DOCKER_TAG
                    sleep 5
                    docker stop test-container
                    docker rm test-container
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        docker push $DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploy to Staging (Ansible)') {
            steps {
                sh 'ansible-playbook ansible_files/deploy_to_k8s.yaml'
            }
        }
    }
}
