pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }
            steps {
                sh '''
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
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''  
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                }
            }
            steps {
                sh '''
                    npm install -g serve
                    nohup node_modules/.bin/serve -s build > serve.log 2>&1 &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/*.xml'
        }
    }
}
