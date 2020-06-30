pipeline {
  agent any
  stages {
    stage('one') {
      parallel {
        stage('one') {
          steps {
            sh 'echo hostname'
          }
        }

        stage('') {
          steps {
            echo 'for one'
          }
        }

      }
    }

    stage('two') {
      steps {
        sleep 2
      }
    }

    stage('three') {
      steps {
        echo 'done in three'
      }
    }

  }
}