pipeline {
    agent {
            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          }

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo "Build Stage"
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test') {
          steps {
            sh '''
              echo "Test Stage"
              [ -f build/index.html ]
              npm run test
            '''
          }
        }
    }

    post {
      always {
        junit 'test-results/junit.xml'
      }
    }
}