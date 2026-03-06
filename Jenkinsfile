pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '8171fa5c-4f0b-4eea-9de4-a6795fe9138c'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    
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
                    echo "-------------- building Node.js LOCALLY"
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
                    sleep 5
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

    // Deploy build using Netlify CLI
       stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                    sh '''
                    echo "-------------- Deploying to Netlify CLI"
                    echo "------------- Added pooling in configuration"
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod --message="Automated deployment from Jenkins pipeline"
                   '''
            }
        }
    }
}
