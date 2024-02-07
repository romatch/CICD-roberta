pipeline {
    agent any
    options {
        timestamps()
    }

    environment {
        DH_NAME = "romkatch"
        FULL_VER = "0.0.$BUILD_NUMBER"
    }
    stages {
        stage('Build') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_user_Cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])
                {
                    sh '''
                    docker login -u $USERNAME -p $PASSWORD
                    docker build -t $DH_NAME/roberta-cicd:$FULL_VER .
                    docker push $DH_NAME/roberta-cicd:$FULL_VER
                    '''
                }
            }
        }
        stage('Trigger Deploy') {
            steps {
                build job: 'RobertaBuild-deploy', wait: false, parameters: [
                    string(name: 'ROBERTA_IMAGE_URL', value: "$DH_NAME/roberta-cicd:$FULL_VER")
                    ]
                }
            }
    }
    post {
        always {
            sh '''
            docker image prune -a --force --filter "until=24h"
            docker builder prune -a --force
            '''
            cleanWs()
        }
    }
}
