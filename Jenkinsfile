pipeline {
  agent any
  options { timestamps() }
  stages {
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    stage('Wavefront Job Dry Run') {
      steps {
        build job: 'Test-Jenkins-Pipeline',
          parameters: [],
          wait: false
      }
    }
  }
}
