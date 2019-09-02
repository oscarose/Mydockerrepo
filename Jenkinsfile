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
    }
    parameters {
        string(name: 'ecrrepo_name', defaultValue: '', description: 'ecr repo name',)
        string(name: 'image_tag', defaultValue: '', description: 'jenkins image version',)
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
                sh """
                ansible-playbook $WORKSPACE/test.yaml --extra-vars "ecrrepo_name=${ecrrepo_name} image_tag=${image_tag}" -vvv
                """ 
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

