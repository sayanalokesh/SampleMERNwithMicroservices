pipeline {
    agent any  

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO_URL = 'https://gallery.ecr.aws/c3w1m1q2/lokesh_mern_hellobe'
        ECR_REGISTRY = 'public.ecr.aws/c3w1m1q2/'
    }

    stages {
        stage('Build Backend Service Image') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/sayanalokesh/SampleMERNwithMicroservices.git', credentialsId: 'gittoken'
                    // sh "${WORKSPACE}/SampleMERNwithMicroservices/"
                    sh 'whoami'
                    sh 'sudo docker-compose up -d'
                    // sh 'docker build -t helloworld-image .'
                    withCredentials([usernamePassword(credentialsId: 'ecr_docker_creds', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        // sh "aws ecr get-login-password --region ${AWS_REGION} | sudo docker login --username AWS --password-stdin ${ECR_REPO_URL}"
                        sh 'docker login ${ECR_REPO_URL} -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}'
                    }
                    
                    // sh 'docker tag helloworld-image:latest ${ECR_REPO_URL}/helloworld-image:latest'
                    sh 'grep -oP "image: \\K.*" docker-compose.yml | xargs -I {} docker push {}'
                }
            }
        }
    }
}
