pipeline {
    agent any

    stages {
        stage('Testing Stage') {
            steps {
                sh '''
                echo "Running tests"
                mkdir -p test-reports
                python -m xmlrunner discover -o test-reports
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
