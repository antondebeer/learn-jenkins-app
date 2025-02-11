pipeline {
    agent any

    stages {
        stage('w/o docker') {
            steps {
                sh '''
                echo "Without Docker"
                ls -la
                touch container-no.txt
                '''
            }
        }
    }
}
