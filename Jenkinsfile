pipeline {
    agent any  

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO_URL = 'https://gallery.ecr.aws/c3w1m1q2'
        ECR_REGISTRY = 'public.ecr.aws/c3w1m1q2/'
    }

    stages {
        stage('Build BE and FE Service Image and push to the ECR') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/sayanalokesh/SampleMERNwithMicroservices.git', credentialsId: 'gittoken'
                    // sh "${WORKSPACE}/SampleMERNwithMicroservices/"
                    sh 'whoami'
                    sh 'sudo docker-compose up -d'
                    // sh 'docker build -t helloworld-image .'
                    // withCredentials([usernamePassword(credentialsId: 'ecr_docker_creds', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    //     // sh "aws ecr get-login-password --region ${AWS_REGION} | sudo docker login --username AWS --password-stdin ${ECR_REPO_URL}"
                    sh '''
                        grep -oP "image: \\K.*" docker-compose.yml | xargs -I {} sudo docker push {}
                    '''
                    // }
                    
                    // sh 'docker tag helloworld-image:latest ${ECR_REPO_URL}/helloworld-image:latest'
                  
                }
            }
        }
    }
}
