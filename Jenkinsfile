pipeline {
    agent any

    environment {
        IMAGE_NAME = 'adnansharief/websites:carvilla'
        NODE_PORT = '30008' // match this with your service.yaml
        EC2_PUBLIC_IP = '13.61.12.117' // change this below
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/adnan-sharief/template-website.git'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-jenkins', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_FILE
                        echo "Deploying image $IMAGE_NAME"

                        sed -i "s|image:.*|image: $IMAGE_NAME|" deployment/deployment.yaml

                        kubectl apply -f deployment/deployment.yaml
                        kubectl apply -f deployment/service.yaml

                        echo "--------------------------------------------"
                        echo "App should be available at:"
                        echo "http://$EC2_PUBLIC_IP:$NODE_PORT"
                        echo "--------------------------------------------"
                    '''
                }
            }
        }
    }
}
