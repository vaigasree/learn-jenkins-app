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
                    sh 'rm -rf node_modules || true'
                    sh 'rm -rf .npm || true'
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

        stage('Test') {
            steps {
                script {
                    try {
                        sh '''
                            cd build/
                            if [ ! -f "index.html" ]; then
                                echo "Error: index.html not found in the build directory."
                                exit 1
                            else
                                echo "Success: index.html found in the build directory."
                            fi
                        '''
                    } catch (exc) {
                        echo "Test failed: ${exc}"
                        throw exc
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
            // Clean up workspace after build
            script {
                sh 'rm -rf node_modules || true'
                sh 'rm -rf .npm || true'
            }
        }
    }
}
