pipeline {
  agent { label 'parallelizable-c7' }
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
          parameters: [
            booleanParam('UPDATE_WAVEFRONT', value: false),
            stringParam('WAVEFRONT_ENDPOINT', value: 'https://snowflake.wavefront.com'),
            booleanParam('FORCE_UPDATE', value: false)
          ],
          wait: false
      }
    }
  }
}
