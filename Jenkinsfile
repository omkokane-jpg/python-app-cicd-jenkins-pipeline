stage('Testing Stage') {
  steps {
    sh '''
    mkdir -p test-reports
    python -m xmlrunner discover -o test-reports
    '''
  }
}

post {
  always {
    junit 'test-reports/*.xml'
  }
}
