pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO_URL = 'https://gallery.ecr.aws/c3w1m1q2/lokesh_mern_hellobe'
    }

    stages {
        stage('Build Backend Service Image') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/sayanalokesh/SampleMERNwithMicroservices.git', credentialsId: 'gittoken'
                    sh "${WORKSPACE}/SampleMERNwithMicroservices/"
                    sh 'docker-compose up'
                    sh 'docker build -t helloworld-image .'
                    sh "aws ecr get-login-password --region ${AWS_REGION} | sudo docker login --username AWS --password-stdin ${ECR_REPO_URL}"
                    sh 'docker tag helloworld-image:latest ${ECR_REPO_URL}/helloworld-image:latest'
                    sh 'docker push ${ECR_REPO_URL}/helloworld-image:latest'
                }
            }
        }
    }
}
