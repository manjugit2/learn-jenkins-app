pipeline {
    agent any
    
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                // Stage to build SW
                sh '''
                    echo "-------------- building Node.js"
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
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "-------------- Checking previous stage build output"
                    test -f 'build/index.html'
                    echo "-------------- starting npm tests"
                    #npm --version
                    node --version
                    npm ci
                    npm test a
                '''
            }
            
            post {
                always {
                    junit 'e2e-test-results/junit.xml'
                }
            }
        }
        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "-------------- Running E2E image"
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            } 
            //from local Manjunath
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: 'play index report', useWrapperFileDirectly: true])
                }
            } 
        }
    }
}
