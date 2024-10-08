pipeline {
    agent any
/*     agent {
            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          } */

    stages {
        stage('Build') {
          agent {
            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          }
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
        stage('Run Tests') {
          parallel {
            stage('Unit Test') {
              agent {
                docker {
                  image 'node:18-alpine'
                  reuseNode true
                }
              }
              steps {
                sh '''
                  echo "Unit Test Stage"
                  [ -f build/index.html ]
                  npm run test
                '''
              }
              post {
                always {
                  junit 'jest-results/junit.xml'
                }
              }
            }

            stage('E2E Test') {
              agent {
                docker {
                  // image 'mcr.microsoft.com/playwright:v1.47.2-noble'
                  image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                  reuseNode true
                }
              }
              steps {
                sh '''
                  echo "E2E Test Stage"
                  npm install serve
                  node_modules/.bin/serve -s build &
                  sleep 10
                  npx playwright test --reporter=html
                '''
              }
              post {
                always {
                  publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
              }
            }
          }
        }
    }
}