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

        // Stage to test E2E
        stage('e2e') {
            agent {
                docker {
                    image 'node:mcr.microsoft.com/playwright:v1.58.2-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "-------------- Running E2E image"
                    npm install serve
                    node_modules/.bin/serve -s build
                    npx playwright test
                '''
            }
        }
    }

    /* 
        stage to review test resulsts irrespective of success or fail
        Results alys provided
    */
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
