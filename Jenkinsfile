// pipeline {
//     agent any

//     stages {
//         stage('Build') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                 ls -la
//                 node --version
//                 npm --version
//                 npm ci
//                 npm run build
//                 ls -la
//                 '''
//             }
//         }

//         stage ('Test') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }

//             steps {
//                 sh '''
//                     test -f build/index.html
//                     npm test
//                 '''
//             }
//         }
//     }

//     post {
//         always {
//             junit 'test-results/junit.xml'
//         }
//     }
// }

pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'de72af54-bf5e-4b89-a4c9-08b1cd5d4730'
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

        stage ('Test') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                        #test -f build/index.html
                            npm test
                        '''
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }

                    // post {
                    //     always {
                    //         publishHTML([
                    //             allowMissing: false, 
                    //             alwaysLinkToLastBuild: false, 
                    //             keepAll: false, 
                    //             reportDir: 'path/to/html/report', 
                    //             reportFiles: 'index.html', 
                    //             reportName: 'HTML Report'
                    //         ])
                    //     }
                    // }
                }
            }
            
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli
                node_modules/.bin/netlify --version
                echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                '''
            }
        }

    }
}