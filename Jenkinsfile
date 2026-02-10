pipeline {
    agent any

    stages {
        stage('Setup & Test') {
            steps {
                sh '''
                echo "Creating virtual environment"
                python3 -m venv venv
                . venv/bin/activate

                echo "Installing requirements"
                pip install --upgrade pip
                pip install -r requirements.txt

                echo "Running tests"
                mkdir -p test-reports
                python3 -m xmlrunner discover -o test-reports
                '''
            }
        }
    }

    post {
        always {
            junit 'test-reports/*.xml'
            echo "We came to an end!"
        }
        failure {
            echo "Sorry mate! build is Failed :("
        }
    }
}
