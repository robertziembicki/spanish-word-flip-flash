pipeline {
    agent any
    
    options {
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:22-alpine'
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('integration test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                    reuseNode true
                }
            }
            steps {
                sh 'npx playwright test'
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                // Mock deployment which does nothing
                echo 'Mock deployment was successful!'
            }
        }

        stage('e2e test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                    reuseNode true
                }
            }
            environment {
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npx playwright test'
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, icon: '', keepAll: false, reportDir: 'reports-e2e/html/', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFilesDirectly: true])
                    junit stdioRetention: 'ALL', testResults: 'reports-e2e/junit.xml'
                }
            }
        }
    }
}