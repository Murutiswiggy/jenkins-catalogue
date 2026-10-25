
pipeline {
    agent {
        node {
            label 'ROBO'
        }
    }

    environment {
        acc_id = '308281189037'
        project = 'robo'
        component = 'catalogue'
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 15, unit: 'MINUTES')
    }

    parameters {
        booleanParam(
            name: 'DEPLOY',
            defaultValue: true,
            description: 'Deploy the application'
        )
    }

    stages {
        stage('Read version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    env.appVersion = packageJson.version.toString()
                    echo "The application version is: ${env.appVersion}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.us-east-1.amazonaws.com

                            docker build -t ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${env.appVersion} .

                            docker push ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${env.appVersion}
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            when {
                expression { params.DEPLOY }
            }
            steps {
                sh 'echo "Deploying"'
            }
        }
    }

    post {
        always {
            echo 'I will always say Hello again!'
        }
        success {
            echo 'I will run when success'
        }
        failure {
            echo 'I will run when it fails'
        }
    }
}
