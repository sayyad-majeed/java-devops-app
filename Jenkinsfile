pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sayyad-majeed/java-devops-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/sayyad-majeed/java-devops-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$TAG .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $DOCKER_IMAGE:$TAG
                    '''
                }
            }
        }

        stage('Update K8s Image') {
            steps {
                sh '''
                sed -i "s|image:.*|image: $DOCKER_IMAGE:$TAG|g" deployment.yaml
                '''
            }
        }

        stage('Deploy to K8s') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
