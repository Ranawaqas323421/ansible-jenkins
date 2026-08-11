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
                sh '''
                    chmod 600 rana_waqas.pem

                    ansible-playbook -i inventory/hosts site.yml \
                      --private-key rana_waqas.pem
                '''
            }
        }
    }
}
