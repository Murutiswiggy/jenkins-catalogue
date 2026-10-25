pipeline {
    agent { 
        node { 
            label 'ROBO' 
        } 
    }
    environment {
        def appVersion = ""
        acc_id = "308281189037"
        project = "robo"
        component = "catalogue"
    }
    options {
        disableConcurrentBuilds()
        timeout(time: 15, unit: 'MINUTES')
    }
    /* parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
        booleanParam(name: 'DEPLOY', defaultValue: true, description: 'Toggle this value')
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    } */
    // Build
    stages {
          stage('Read version'){
            steps{
                script {
                    def packageJson = readJSON file: 'package.json'
                    // Extract the version property
                    appVersion = packageJson.version
                    echo "The application version is: ${appVersion}"
                }
            }
        }   
        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                        npm install
                    """
                } 
            }
        }
        
stage('Docker Build') {
    steps {
        script {
            // AWS authentication
            withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                sh """
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.us-east-1.amazonaws.com

                    docker build -t ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion} .

                    docker push ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion}
                """
            }
        }
    }
}


stage('Deploy') {
    when {
        expression {
            params.DEPLOY == true || params.DEPLOY == 'true'
        }
    }

    /*
    input {
        message "Should we continue?"
        ok "Yes, we should."
        submitter "alice,bob"
        parameters {
            string(
                name: 'PERSON',
                defaultValue: 'Mr Jenkins',
                description: 'Who should I say hello to?'
            )
        }
    }
    */

    steps {
        script {
            sh '''
                echo "Deploying"
            '''
        }
    }
}


    } // closes stages

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
    } // closes post
} // closes pipeline