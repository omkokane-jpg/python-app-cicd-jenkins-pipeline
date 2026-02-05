pipeline {
    agent {
        docker {
            image 'python:3.11'
        }
    }

    stages {

        stage('Clean Reports') {
            steps {
                sh '''
                echo '********* Cleaning Workspace Stage Started **********'
                rm -rf test-reports/
                mkdir -p test-reports
                echo '********* Cleaning Workspace Stage Finished **********'
                '''
            }
        }

        stage('Build Stage') {
            steps {
                sh '''
                echo '********* Build Stage Started **********'
                python -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pyinstaller --onefile app.py
                echo '********* Build Stage Finished **********'
                '''
            }
        }

        stage('Testing Stage') {
            steps {
                sh '''
                echo '********* Test Stage Started **********'
                . venv/bin/activate
                python test.py
                echo '********* Test Stage Finished **********'
                '''
            }
        }

        stage('Configure Artifactory') {
            steps {
                script {
                    echo '********* Configure Artifactory Started **********'
                    def userInput = input(
                        id: 'userInput',
                        message: 'Enter password for Artifactory',
                        parameters: [
                            [$class: 'PasswordParameterDefinition',
                             defaultValue: '',
                             description: 'Artifactory Password',
                             name: 'password']
                        ]
                    )

                    sh """
                    jfrog rt c artifactory-demo \
                    --url=http://34.68.191.118:8081/artifactory \
                    --user=admin \
                    --password=${userInput}
                    """

                    echo '********* Configure Artifactory Finished **********'
                }
            }
        }

        stage('Sanity Check') {
            steps {
                input "Does the staging environment look ok?"
            }
        }

        stage('Deployment Stage') {
            steps {
                input "Do you want to Deploy the application?"
                sh '''
                echo '********* Deploy Stage Started **********'
                . venv/bin/activate
                timeout 60 python app.py
                echo '********* Deploy Stage Finished **********'
                '''
            }
        }
    }

    post {
        always {
            echo 'We came to an end!'
            archiveArtifacts artifacts: 'dist/*', fingerprint: true

            junit allowEmptyResults: true, testResults: 'test-reports/*.xml'

            deleteDir()
        }

        success {
            echo 'Build Successful!!'
        }

        failure {
            echo 'Sorry mate! build is Failed :('
        }

        unstable {
            echo 'Run was marked as unstable'
        }

        changed {
            echo 'Pipeline state changed.'
        }
    }
}
