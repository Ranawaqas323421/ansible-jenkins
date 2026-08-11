pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Umair1012/ansible-collections.git'
            }
        }

        stage('Install Collections') {
            steps {
                sh 'ansible-galaxy collection install -r requirements.yml'
            }
        }

        stage('Run Playbook') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'rana_waqas.pem',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ansible-playbook -i inventory/hosts site.yml \
                          --private-key "$SSH_KEY"
                    '''
                }
            }
        }
    }
}
