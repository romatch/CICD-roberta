pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                docker build
                docker tag
                docker push

                '''
                sh 'ls'
                sh 'echo building...'
            }
        }
    }
}
