pipeline {
    agent {
        label'master'
    }
    environment {
        awsRegion = 'us-east-1'
        ecrAWSAccountIdProd = '187212085277'
        ecrRepositoryName = "testdocker"
        //ecrRepositoryName = 'testdocker' + ':' + "${imageVersion}"
        ecrRegistryUrl = "${ecrAWSAccountIdProd}.dkr.ecr.${awsRegion}.amazonaws.com"
        ecrRepositoryFQN = "${ecrRegistryUrl}/${ecrRepositoryName}" 
        imageVersion = "${ImageVersion}"
        //def imageVersion = "${params.ImageVersion}"
    }
    parameters {
        string(name: 'ImageVerson', defaultValue: '', description: 'jenkins image version',)
    }
    stages {
        stage('send email notification') {
            steps {
                emailext (
                    subject: "Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",                    
                    body: """TestDocker container image build is in Progress, Check console output at "${env.BUILD_URL}" and monitor the console log. """,
                    to: "abrahamdimma@gmail.com",
                    from: "abraham.ogba@yahoo.com"
                    )
            }
        }
        stage('checkout Dockerfile from scm') {
            steps {
                git branch: 'master',
                    credentialsId: 'github_jenkins',
                        url: 'https://github.com/oscarose/Mydockerrepo.git'
            }
        }
        stage('build TestDocker docker image') {
            steps {
                sh label: '', script: '''#!/usr/bin/env bash
                     cd $WORKSPACE
                     docker build . -t ${ecrRepositoryName}:${imageVersion} --no-cache --pull
                     docker tag ${ecrRepositoryName}:${imageVersion} ${ecrRepositoryFQN}:${imageVersion}
                     $(aws ecr get-login --no-include-email --region us-east-1)
                     docker push ${ecrRepositoryFQN}:${imageVersion}
                     docker rmi ${ecrRepositoryName}:${imageVersion}
                     '''
            }
        }
        stage('send success email notification') {
            steps {
                emailext (
                    subject: "Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",
                    body: """Successful TestDocker container image build""",
                    to: "Abrahamdimma@gmail.com",
                    from: "Abraham.ogba@yahoo.com"
                    )
            }
        }
    }
}

