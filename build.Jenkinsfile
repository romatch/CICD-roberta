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
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')])
                {
                    sh '''
                    snyk auth $SNYK_TOKEN
                    snyk ignore --severity=high,critical --all
                    snyk container test $DH_NAME/roberta-cicd:$FULL_VER --file=Dockerfile
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
            docker system prune -a --force --filter "until=24h"
            docker builder prune -a --force
            '''
            cleanWs()
        }
    }
}

