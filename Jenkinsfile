pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

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
            junit allowEmptyResults: true,
                  testResults: 'test-reports/*.xml',
                  skipMarkingBuildUnstable: true
            echo "We came to an end!"
        }
    }
}
