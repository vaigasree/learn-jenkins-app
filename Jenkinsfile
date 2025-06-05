pipeline {
    agent any

    environment {
        // Set a custom cache directory for npm that the Jenkins user can access
        NPM_CONFIG_CACHE = "${env.WORKSPACE}/.npm"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                script {
                    // Clean the workspace before starting
                    sh 'sudo rm -rf node_modules || true'
                    sh 'sudo rm -rf .npm || true'
                }
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root' // Run as root to avoid permission issues
                }
            }
            steps {
                script {
                    try {
                        sh '''
                            ls -la
                            node --version
                            npm --version
                            npm ci --cache ${NPM_CONFIG_CACHE}
                            npm run build
                            ls -la
                        '''
                    } catch (exc) {
                        echo "Build failed: ${exc}"
                        throw exc
                    }
                }
            }
        }
        stage('Test'0) {
            steps {
                script {
                    sh '''
                        cd build/
                        test -f index.html
                        '''
                }
            }
        }
    }

    post {
        always {
            echo 'Build stage completed.'
            // Clean up workspace after build
            script {
                sh 'sudo rm -rf node_modules || true'
                sh 'sudo rm -rf .npm || true'
            }
        }
    }
}
