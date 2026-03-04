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

        // Stage to test
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
