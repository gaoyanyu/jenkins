pipeline {
  agent any
  stages {
    stage('stage') {
      steps {
        sh 'echo "git clone codes"'
      }
    }

    stage('stage2') {
      parallel {
        stage('stage2') {
          steps {
            echo 'stage 2'
          }
        }

        stage('') {
          agent {
            docker {
              image 'busybox:latest'
            }

          }
          steps {
            sleep 3
          }
        }

      }
    }

    stage('stage3') {
      agent {
        node {
          label 'test'
        }

      }
      steps {
        sh 'echo hostname > hostname.txt'
      }
    }

    stage('stage4') {
      steps {
        echo 'end'
      }
    }

  }
}