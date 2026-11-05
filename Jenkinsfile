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

        stage('test') {
            parallel {
                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:22-alpine'
                        }
                    }
                    steps {
                        sh 'npm ci'
                        sh 'npx vitest run --reporter=verbose'
                    }
                }
                stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                        }
                    }
                    steps {
                        sh 'npm ci'
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                }
            }
            environment {
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npm ci'
                sh 'npx playwright test'
            }
            post {
                always {
                    publishHTML([
                            allowMissing: false,
                            alwaysLinkToLastBuild: true,
                            keepAll: false,
                            reportDir: 'reports-e2e/html/',
                            reportFiles: 'index.html', // <-- Das hat gefehlt!
                            reportName: 'Playwright HTML Report',
                            reportTitles: '',
                            useWrapperFileDirectly: true
                    ])
                    junit stdioRetention: 'ALL', testResults: 'reports-e2e/junit.xml'
                }
            }
        }
    }
}