pipeline {
  agent any
  options { timestamps() }
  stages {
    stage('checkout') {
      scmInfo = checkout([
                $class: 'GitSCM',
                branches: scm.branches,
                extensions: scm.extensions,
                userRemoteConfigs:
                  [[url: scm.userRemoteConfigs.url[0],
                    name: scm.userRemoteConfigs.name[0],
                    refspec: "+refs/pull/*:refs/remotes/origin/pr/*",
                    credentialsId: scm.userRemoteConfigs.credentialsId[0]
                  ]]
                ])
      println("${scmInfo}")
      env.GIT_BRANCH = scmInfo.GIT_BRANCH
    }
    stage('Wavefront Job Dry Run') {
      steps {
        script {
          echo "Checking if files have changed for this particular directory and trigger the Wavefront dry run job"
          def changedCommand = "git diff origin/${env.CHANGE_TARGET} --name-only"
          if ("${env.GIT_PREVIOUS_COMMIT}" != "null" && "${env.GIT_PREVIOUS_COMMIT}" != "${env.GIT_COMMIT}") {
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
