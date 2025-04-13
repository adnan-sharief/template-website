pipeline {
    agent any

    environment {
        IMAGE_NAME = 'adnansharief/websites:carvilla'
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

                        # Replace image in deployment YAML
                        sed -i "s|image:.*|image: $IMAGE_NAME|" deployment/deployment.yaml

                        kubectl apply -f deployment/deployment.yaml
                        kubectl apply -f deployment/service.yaml

                        echo "Waiting for LoadBalancer external IP..."
                        external_ip=""
                        for i in {1..30}; do
                            external_ip=$(kubectl get svc my-app-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
                            if [ -n "$external_ip" ]; then
                                break
                            fi
                            echo "Still waiting... (${i}s)"
                            sleep 2
                        done

                        if [ -n "$external_ip" ]; then
                            echo ""
                            echo "Your app is live at: http://$external_ip"
                            echo ""
                        else
                            echo "Failed to get external IP after waiting. Check service or cloud provider settings."
                        fi

                    '''
                }
            }
        }
    }
}
