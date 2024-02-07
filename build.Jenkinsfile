pipeline {
    agent any
    options {
        timestamps()
        retry(1)
    }
    environment {
        DU_NAME = "romkatch"
    }
    stages {
        stage('Build') {
            steps {
                sh 'ls'
                sh 'echo building.....'
                withCredentials([usernamePassword(credentialsId: 'docker_user_Cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])
                {
                    sh '''
                       docker login -u $USERNAME -p $PASSWORD
                       docker build -t $DU_NAME/roberta-cicd:0.0.$BUILD_NUMBER .
                       docker push $DU_NAME/roberta-cicd:0.0.$BUILD_NUMBER
                       '''
                }

            }
            post {
                always{
                    sh 'docker image prune -a --force --filter "until=24h"'
                }
            }
            post {
                always{
                    cleanWs()
                }
            }
        }
        stage('Trigger Deploy') {
            steps {
                build job: 'RobertaBuild-deploy', wait: false, parameters: [
                    string(name: 'ROBERTA_IMAGE_URL', value: "$DU_NAME/roberta-cicd:0.0.$BUILD_NUMBER")
                    ]
                }
            }
    }
}