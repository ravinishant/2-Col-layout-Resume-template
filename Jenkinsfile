pipeline {
  agent any
  options { timestamps() }
  stages {
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    // stage('Wavefront Job Dry Run') {
    //   steps {
    //     build job: 'Test-Jenkins-Pipeline',
    //       parameters: [],
    //       wait: false
    //   }
    // }
    stage('Wavefront Job Dry Run') {
      steps {
        script {
          echo "Checking if files have changed for this particular directory and trigger the Wavefront dry run job"
          def changedCommand = "git diff origin/${env.CHANGE_TARGET} --name-only"
          if ("${env.GIT_PREVIOUS_COMMIT}" != "${env.GIT_COMMIT}") {
            // make sure previous commit is still a valid object
            def statusCode = sh (script: "git cat-file -e ${env.GIT_PREVIOUS_COMMIT}", returnStatus: true)
            if (statusCode == 0) {
              changedCommand = "git diff ${env.GIT_COMMIT} ${env.GIT_PREVIOUS_COMMIT} --name-only"
            }
          }
          def dirNames = sh(script: """
          |CHANGED=`${changedCommand}`
          |IFS=\$'\n'
          |for f in \$CHANGED; do
          | if [[ \$f == src/* ]]; then
          |   echo "wavefront_alerts"
          | else
          |   echo \${f%%/*}
          | fi
          |done
          """.stripMargin(), returnStdout: true).trim().split()
          // remove duplicates
          dirNames = dirNames.toUnique()
          for (dirName in dirNames) {
            echo "printing dirNames"
            println("${dirName}")
            if (dirName.toLowerCase() == "wavefront_alerts") {
              build job: 'Test-Jenkins-Pipeline',
              parameters: [],
              wait: true
            }
          }
        }
      }
    }
  }
}
