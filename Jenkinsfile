pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"                     // or AWS ECR URL
        IMAGE_NAME = "rahulrk410/my-sample-app"    // change to your DockerHub repo
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/RAHUL-RK410/My-Sample-Repo.git'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'echo "Running unit tests..."'
                // 👉 Replace this with your project test command
                sh 'echo "No tests configured yet"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
                }
            }
        }

        stage('Push to Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push $IMAGE_NAME:$IMAGE_TAG"
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sshagent(['staging-server-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@STAGING_SERVER "
                        docker pull $IMAGE_NAME:$IMAGE_TAG &&
                        docker stop myapp || true &&
                        docker rm myapp || true &&
                        docker run -d --name myapp -p 8080:8080 $IMAGE_NAME:$IMAGE_TAG
                    "
                    '''
                }
            }
        }
    }
}
