pipeline {
    agent any
 
    tools {
        jdk "JDK21"
        maven "maven"  
    }
    environment {
        EC2_HOST = "ubuntu@54.91.24.130"
    }
 
    stages{
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khushig222/Boardgame.git'
            }
        }
        stage('Deploy to EKS') {
            steps {
               withCredentials([sshUserPrivateKey(credentialsId: 'SSH_KEY', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                    ssh -i \$SSH_KEY -o StrictHostKeyChecking=no \$EC2_HOST '
                      kubectl get nodes
                      sudo rm -rf Boardgame || true
                      git clone https://github.com/khushig222/Boardgame.git
                      cd Boardgame
 
                      kubectl apply -f deployment-service.yaml
                    '
                    """
                }
            }
        }
         
}
}
