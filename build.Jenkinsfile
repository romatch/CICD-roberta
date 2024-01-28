pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh'''

                    docker login --password $TOKEN
                    '''
                    sh 'ls'
                    sh 'echo building...'
            }
        }
    }
}
stages {
        stage('push to ecr') {
            steps {
                sh'''
                docker tag
                docker push

            }
        }
    }
}
